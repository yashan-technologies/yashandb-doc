## 概述

基于预留的I/O Fencing方法利用存储设备支持的持久预留命令为共享集群/分布式集群构建I/O栅栏，具有绝对的安全性和更优的RTO指标。

I/O Fencing依托存储预留机制实现，YashanDB兼容两类标准方案：SCSI协议栈的SCSI持久预留、NVMe协议栈的NVMe预留。

### SCSI持久预留

SCSI持久预留（SCSI PR，SCSI Persistent Reservation）是SCSI-3/SPC-3及以上协议定义的、面向多服务器共享块设备的集群访问控制与故障隔离机制，是传统FC/iSCSI SAN存储实现高可用集群的标准方案。其核心工作流程如下：

1. **注册（Register）**：服务器向存储设备发送`PERSISTENT RESERVE REGISTER`命令，以唯一的密钥（Reservation Key）在设备上完成身份注册，成为 “注册者（Registrant）”。

2. **预留（Reserve）**：服务器通过发送`PERSISTENT RESERVE`命令，申请特定类型的访问权限。典型场景下使用Type 5（Write Exclusive – Registrants Only），仅允许已注册服务器对设备执行写操作，实现访问隔离。

3. **抢占/清除（Preempt/Clear）**：当节点故障时，存活节点可通过`PERSISTENT RESERVE PREEMPT`命令，强制抢占并清除故障节点的预留，使其无法继续下发I/O请求，从而完成I/O Fencing，保障数据一致性。

### NVMe预留

NVMe预留（NVMe Reservation）是NVMe 1.2及以上协议原生支持的、面向多服务器共享NVMe设备（PCIe直连/NVMe-oF）的集群访问控制与故障隔离机制，是NVMe架构下替代SCSI PR的原生方案。其核心工作流程如下：

1. **注册（Register）**：服务器向NVMe Controller发送`Reservation Register`命令，使用服务器唯一的Host ID在设备上完成注册。

2. **预留（Reserve）**：服务器通过`Reservation Acquire`命令，申请特定类型的预留权限，例如Write Exclusive Registrants Only类型，仅允许已注册服务器执行写操作，实现访问控制。

3. **抢占/清除（Preempt/Clear）**：当节点故障时，存活节点可通过`Reservation Preempt`命令，强制抢占并清除故障节点的预留，使其I/O请求被设备拒绝，实现I/O Fencing。


## 硬件与环境要求

| 要求| SCSI持久预留| NVMe预留|
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 存储设备 | 存储设备支持SCSI-3协议及以上和SPC-3命令集（SCSI Primary Commands-3）及以上，且支持类型为5的持久预留（Write Exclusive – Registrants Only） | 存储设备采用PCIe直连或NVMe-oF RDMA方式连接<br />存储设备支持NVMe 1.3协议（及以上）和NVMe预留命令集、控制器（Controller）和命名空间（Namespace）均支持NVMe预留功能，并且未开启Dispersed Namespace |
| 存储设备配置 | 推荐采用多路径绑定LUN，并在每台服务器上完成多路径配置文件修改 | 共享集群/分布式集群中每台服务器已配置唯一的ID<br />NVMe设备不能开启Dispersed Namespace |
| YCSRA进程 | 需要YCSRA进程在线以提供特权操作代理服务，请确保YCSRA进程在每个服务器上都已启动，否则请参考[ycsrootagent工具](../../../工具手册/ycsrootagent)手动sudo拉起。为简化运维，建议在每台服务器上为该进程配置开机自启动，具体操作请查阅[配置开机自启动](../../../安装和升级/安装部署/安装后初始环境/配置开机自启动.md)。 | 同SCSI持久预留 |

## 配置和使用

部署共享集群/分布式集群时，yasboot会自动选择最优的fence类型，一般无需手动配置。

> **Caution**:
>
> 以下操作会停止集群并重启，请提前确认业务已停止或可接受停止。

1. 确认业务已停止或可接受停止，然后关闭数据库集群。

    ```shell
    $ yasboot cluster stop -c yashandb
    ```

2. 启动yasfs服务。

    ```shell
    $ yasfs &
    ```

3. 执行[ycsctl set_ycr](../../../工具手册/ycsctl/ycsctl使用指导/集群配置命令)命令配置fence类型。

    ```shell
    ycsctl set_ycr FENCE_TYPE 2
    ```

4. 停止yasfs服务。

    ```shell
    $ yfscmd exec "shutdown abort"
    ```

5. 启动数据库集群。

    ```shell
    $ yasboot cluster start -c yashandb
    ```

6. 检查并拉起[YCSRA进程](../../../工具手册/ycsrootagent)。

    ```shell
    $ top -c | grep YCSRA

    # 若不存在，则需手动拉起
    $ sudo ycsrootagent start -H /data/yashan/yasdb_data/ycs/ce-1-1&
    YCSRA instance start successfully.
    ```

7. 查询fence状态。

    > **Note**:
    >
    > ycsctl show fence命令需要[YCSRA进程](../../../工具手册/ycsrootagent)在线才能正常返回结果。

    ```shell
    $ ycsctl show fence
    ```

## 常见问题

### 1. 开启基于预留的I/O Fencing后，yfscmd添加磁盘失败

可能原因及解决方法：

- 错误码YAS-00304：目标磁盘不是块存储设备或不支持相应的预留协议。使用fenceResvCheck.sh脚本进一步检测和验证。

- 错误码YAS-00341：目标磁盘残留了非本共享集群/分布式集群注册或预留的信息。在确认安全后使用[ycsrootagent scsi clear命令](../../../工具手册/ycsrootagent.md)清理残留信息（请谨慎操作）。

### 2. 硬件条件均已满足，但仍未成功开启NVMe预留，是什么原因？

可能原因：共享集群/分布式集群中服务器的ID未配置或配置不正确。

解决方法：

1. 检查各服务器上是否存在`/etc/nvme/hostid`文件，如不存在请使用`uuidgen`命令创建，具体操作请查阅[配置安装环境](../../../安装和升级/安装部署/安装前准备/配置安装环境.md)。

2. 检查各服务器的ID是否唯一。

3. 如修改过服务器ID，需要重新连接NVMe设备。

<span id="fenceResvCheck_usage" name="fenceResvCheck_usage"></span>

## 附录：硬件能力检测脚本

fenceResvCheck.sh脚本能够自动化检测特定存储设备是否满足基于预留的I/O Fencing的硬件要求。

- 对于SCSI设备：检测存储设备是否支持SCSI-3协议、SPC-3命令集和类型为5的持久预留。

- 对于NVMe设备：检测存储设备是否支持NVMe 1.3协议和NVMe预留命令集、控制器（Controller）和命名空间（Namespace）是否均支持NVMe预留功能。

### 使用说明

fenceResvCheck脚本存放在`$YASDB_HOME/scripts`目录，需要使用root用户（或sudo）执行，支持单服务器检测和双服务器检测两种模式。

- 单服务器检测：仅在执行脚本的服务器上执行检测。

- 双服务器检测：在执行脚本的服务器和-i选项指定的辅助服务器上执行检测，可获得更准确可靠的结果。

### 依赖项

- [ycsrootagent工具](../../../工具手册/ycsrootagent)

- mpathpersist：服务器使用DM-Multipath多路径软件管理共享存储时需要

- sshpass：需要执行双服务器检测且未配置免密登录时需要

### 参数说明

|  选项| 含义|
| ------------------ | ---------------------------------------------------- |
| *-d* | 操作的磁盘设备名，多个设备名用逗号隔开，必须为绝对路径，不可省略。单个设备名最长31个字符 |
| *-i* | 运行双服务器检测必须指定的辅助服务器IP地址，此地址必须可ping通 |
| *--port* | 辅助服务器的SSH连接端口，省略则默认使用22 |
| *-u* | 登录辅助服务器的用户名，此用户必须有辅助服务器的sudo免密权限 |
| *-p* | 登录辅助服务器的密码，若不指定，将尝试免密登录。推荐为-u指定的用户配置免密登录 |

> **Note**:
>
> 为避免暴露明文密码，推荐为-u指定的用户配置免密登录。
>
> 若-d指定的磁盘上有残留的持久预留信息，本脚本会自动将其清除，因此执行脚本前请确认磁盘已空闲。

### 使用示例

```shell
$ sudo ./fenceResvCheck.sh -d /dev/yfs/DISK_NAME1 -i 192.168.1.123 -u yashan -p password
```

检测结果中的`Supportive Device`将列示满足硬件要求的磁盘路径，`Unsupportive Device`则列示不满足硬件要求的磁盘路径。

若脚本检测结果显示有磁盘不满足硬件要求，请更换能力匹配的共享存储设备或者考虑使用其他类型的I/O Fencing方法。

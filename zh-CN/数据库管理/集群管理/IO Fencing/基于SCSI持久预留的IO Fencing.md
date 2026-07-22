基于SCSI持久预留（Persistent Reservation，后简称PR）的I/O Fencing方法（后简称SCSI I/O Fencing）利用共享存储设备普遍支持的SCSI持久预留命令为共享集群构建I/O栅栏，具有绝对的安全性和更优的RTO指标。

此方法需要YCSRA进程在线以提供特权操作代理服务，请确保YCSRA进程在每个服务器上都已启动，否则请参考[ycsrootagent工具](../../../工具手册/ycsrootagent)手动sudo拉起。为简化运维，建议在每台服务器上为该进程配置开机自启动，具体操作请查阅[配置开机自启动](../../../安装和升级/安装部署/安装后初始环境/配置开机自启动.md)。

- 类型码：2

- 硬件要求：共享存储设备支持SCSI-3协议（及以上）和SPC-3命令集（SCSI Primary Commands-3，及以上），且支持类型5的持久预留（Write Exclusive – Registrants Only）。

- 硬件能力检测脚本：[fenceScsiCheck.sh](#fenceScsiCheck_usage)。

- 注意事项：若已通过多路径的方式挂载磁阵，请在配置此方法前[修改多路径配置文件](../../../安装和升级/安装部署/安装前准备/配置存储设备.md#modify_multipath_conf)。

## 基本原理

PR命令集主要包含注册、预留、抢占、释放和查询等子命令。通常情况下，由共享集群的主服务器执行注册和预留，其他成员服务器执行注册，使得仅集群成员服务器具备共享存储的写权限。

主服务器在更新集群成员关系前会执行抢占，以清除被驱逐服务器的key，使其无法再写数据到共享存储。故障服务器恢复并重新加入集群后，再次执行注册并获得写权限。

## 常见问题

#### 开启SCSI I/O Fencing后，yfscmd添加磁盘等操作失败

- 错误码YAS-00304：磁盘不是SCSI块存储设备或不支持SCSI-3及以上协议或不支持持久预留子命令，可使用[fenceScsiCheck脚本](#fenceScsiCheck_usage)进一步检测和验证。

- 错误码YAS-00341：磁盘是SCSI设备但残留了非本共享集群注册或预留的信息，请在确认安全后谨慎使用[ycsrootagent scsi clear命令](../../../工具手册/ycsrootagent.md#scsi_clear)清理残留信息。

<span id="fenceScsiCheck_usage" name="fenceScsiCheck_usage"></span>

## 附录：硬件能力检测脚本

fenceScsiCheck.sh脚本能够自动化检测特定存储设备是否满足SCSI I/O Fencing的硬件要求（支持SCSI-3协议、SPC-3命令集和类型5的持久预留）。

### 使用说明

fenceScsiCheck脚本存放在`$YASDB_HOME/scripts`目录，需要在root下（或sudo）执行，支持单服务器检测和双服务器检测两种模式。

- 单服务器检测：仅在执行脚本的服务器上执行检测，可检测磁盘是否为SCSI块存储设备、是否支持注册和预留两种子命令以及是否支持类型5的持久预留。

- 双服务器检测：在执行脚本的服务器和-i选项指定的辅助服务器上执行检测，可检测磁盘是否为SCSI块存储设备、是否支持所有持久预留子命令以及是否支持类型5的持久预留。

通常情况下，建议通过-i选项指定一台辅助服务器执行双服务器检测，以获得更准确可靠的检测结果。

|  选项| 含义|
| ------------------ | ---------------------------------------------------- |
| *-d* | 操作的磁盘设备名，多个设备名用逗号隔开，必须为绝对路径，不可省略。单个设备名最长31个字符 |
| *-i* | 运行双服务器检测必须指定的辅助服务器IP地址，此地址必须可ping通 |
| *--port*              | 辅助服务器的SSH连接端口，省略则默认使用22            |
| *-u* | 登录辅助服务器的用户名，若不指定，将尝试使用当前执行脚本的用户名登录。此用户必须有辅助服务器的sudo免密权限 |
| *-p* | 登录辅助服务器的密码，若不指定，将尝试免密登录。推荐为-u指定的用户配置免密登录 |

> **Note**: 
>
> 为避免暴露明文密码，推荐为-u指定的用户配置免密登录。
>
> 若-d指定的磁盘上有残留的持久预留信息，本脚本会自动将其清除，因此执行脚本前请确认磁盘已空闲。

### 依赖项

本脚本主要依赖[ycsrootagent工具](../../../工具手册/ycsrootagent)，并在某些情况下依赖下述工具：

- mpathpersist：服务器使用DM-Multipath多路径软件管理共享存储时，脚本依赖mpathpersist。通常multipath-tools软件包会一同安装mpathpersist，可通过`mpathpersist --help`确认工具是否可用。

- sshpass：需要执行双服务器检测且-u指定的用户未配置免密登录时，脚本依赖sshpass，可通过`sshpass -h`确认工具是否可用。通常更推荐用户为服务器配置免密登录。

### 使用示例

当共享集群配置了SCSI I/O Fencing，建议用户在使用[yfscmd磁盘管理命令](../../../工具手册/yfscmd/磁盘管理命令)添加新的磁盘到集群文件系统前手动执行本脚本对设备进行能力检测。

本文以需对磁盘/dev/yfs/DISK_NAME1和/dev/yfs/DISK_NAME2进行双服务器能力检测为例，辅助测试机的ip为192.168.1.123，登录用户为yashan，操作时请替换成实际的设备路径、用户名及其登录方式。

```shell
$ sudo ./fenceScsiCheck.sh -d /dev/yfs/DISK_NAME1,/dev/yfs/DISK_NAME2 -i 192.168.1.123 -u yashan -p password
SCSI-based I/O Fencing Test(Dual-host) Start
Test Running on /dev/yfs/DISK_NAME1...
Device /dev/yfs/DISK_NAME1 Support SCSI-based I/O Fencing
Test Running on /dev/yfs/DISK_NAME2...
Warning: failed to register and reserve, maybe /dev/yfs/DISK_NAME2 is not scsi block device or not support persistent reservation subcommand
Supportive Device: /dev/yfs/DISK_NAME1
Unsupportive Device: /dev/yfs/DISK_NAME2
Test failed
```

检测结果中的`Supportive Device`将列示满足SCSI I/O Fencing硬件要求的磁盘路径，`Unsupportive Device`则列示不满足SCSI I/O Fencing硬件要求的磁盘路径，请根据磁盘路径查找测试过程中返回的Warning信息了解更多详情。

若脚本检测结果显示有磁盘不满足硬件要求，请更换能力匹配的共享存储设备或者考虑使用其他类型的I/O Fencing方法。

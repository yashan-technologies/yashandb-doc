## 概述

在途I/O保护算法是YCS提供的无硬件依赖的通用I/O Fencing方法，若部署共享集群/分布式集群所用的[存储设备](../../../安装和升级/安装部署/安装前准备/服务器准备.md#Storage)无法满足基于预留的I/O Fencing的要求，默认采用在途I/O保护算法。

共享集群/分布式集群数据库实例和集群文件系统会按一定的策略将本实例发起的在途I/O数持久化到投票盘。若所属服务器被集群驱逐，集群数据库实例会主动阻断新的I/O请求，并在旧的在途I/O处理完成后安全退出。

主服务器在更新集群成员关系前若发现被驱逐服务器的数据库实例或集群文件系统有在途I/O则会等待其安全退出或直到超时。

相较于依赖硬件能力的I/O Fencing方法，在途I/O保护算法会一定程度上延长集群RTO。

> **Caution**:
>
> 在途IO保护算法并不能拦截所有的在途IO，使用该方法存在脑裂风险，**推荐使用**[基于预留的IO Fencing](基于预留的IO Fencing)。


## 硬件与环境要求

无需依赖硬件设备及其配置。

当存储设备I/O性能差或I/O卡顿现象频发时，需调大共享集群/分布式集群的DISK_HB_KEEP_ALIVE参数配置值以确保算法的可靠性，DISK_HB_KEEP_ALIVE参数配置需大于共享存储设备I/O的最大理论延迟。

## 配置和使用

部署共享集群/分布式集群时，yasboot会自动选择最优的fence类型，一般无需手动配置。

> **Caution**:
>
> 以下操作会停止数据库集群并重启，请提前确认业务已停止或可接受停止。


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
    ycsctl set_ycr FENCE_TYPE 1
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

### 集群数据库自我fence

告警日志中出现YcsDbFenced告警事件，可能是共享集群/分布式集群数据库实例在服务器被驱逐后阻断了I/O请求，请参考[告警事件说明](../../../参考手册/告警事件)进行故障排查和处理。

## 功能简介

虚拟IP（VIP，Virtual IP）是由YCS管理的一种节点服务资源，数据库实例监听VIP并提供服务。VIP与实例监听地址（LISTEN_ADDR）的差异在于，VIP可以在故障实例和正常实例之间迁移使得实例级故障对用户透明。

当某个集群节点发生宕机等故障时，YCS首先会进行投票仲裁将故障节点驱逐出集群，再按照高可用和均匀分布的原则将故障节点的VIP资源重新分布到正常节点。正常节点接管故障的VIP后，会在该节点运行的服务器上重新启动该VIP并在网络层将连接该VIP的请求和流量重定向到当前服务器，同时，该节点管理的数据库实例也会动态地添加对该VIP的监听。经过上述故障转移流程，故障实例的VIP会快速恢复连接可用，此次集群故障也会在最大程度上对客户的业务透明。

VIP的启停和高可用管理需要YCSRA进程在线以提供特权操作代理服务，请确保YCSRA进程在每个服务器上都已启动，否则请参考[ycsrootagent工具](../../工具手册/ycsrootagent)手动sudo拉起。为简化运维，建议在每台服务器上为该进程配置开机自启动，具体操作请查阅[配置开机自启动](../../安装和升级/安装部署/安装后初始环境/配置开机自启动.md)。

> **Note**: 
>
> 如需在运行着VIP的集群服务器上重启网络，请先手动执行ycsctl stop vip停止该节点正在运行的VIP，待网络重启完成后，再执行ycsctl start vip重新启动VIP。

## 配置要求



- 服务器的网卡必须为以太网卡，且支持ARP/NDP协议。

- 必须先为集群配置公网信息（安装部署时yasboot package ce gen命令指定--public-network参数或安装完成后执行ycsctl add network命令指定）后才能使用VIP。

- 同一共享集群中的所有节点必须采用统一的VIP配置策略（主备集群之间VIP配置策略可以不同）：

    - 节点统一配置VIP或不配置。

    - VIP统一采用IPv4或IPv6。

- VIP地址应规划为预留的空闲IP地址。

- VIP必须属于公网子网，且必须与实例监听地址（LISTEN_ADDR）处于相同子网。



<span id="vip_configuration" name="vip_configuration"></span>

## 为已有集群配置VIP

用户在安装部署共享集群时未配置VIP，或者从不支持（或未开启）VIP的旧版本集群升级到支持VIP的新版本集群，可以按照如下流程为集群配置VIP资源。

### 前提条件

- 当前数据库版本为23.4.2.100及以上，或已升级到该版本及以上。

- 已规划符合VIP配置要求的网段和IP地址。



### 步骤1：配置公网子网 

1. 以安装用户登录数据库安装服务器。


2. 查看集群信息，确认是否已配置公网和服务端口。

    ```shell
    # 查看集群YCR配置信息
    $ ycsctl show config
        Cluster name: yashandb, config version: 4
        ……
        
        Default resource yasfs: enabled
        Resource SCAN: disabled
        Resource vip: disabled
        ……

        Nodes in cluster:
        Node name: yas1, yascs/yasfs inter connect URL: 172.16.1.2:1788, Node ID: 1
            public service port: 1688
            yasdb instance name:yasdb-1-1, yasdb instance id:1
        Node name: yas2, yascs/yasfs inter connect URL: 172.16.1.3:1788, Node ID: 2
            yasdb instance name:yasdb-1-2, yasdb instance id:1
    ```
    回显信息中没有Network信息表示暂未配置公网，节点yas2下没有public service port表示该节点暂未配置服务端口号。

3. （可选）若未配置公网，需执行如下操作：

    ```shell
    $ ycsctl add network -subnet 192.168.1.0/24/ens192
    ```



### 步骤2（可选）：配置节点服务端口号

若有节点未配置服务端口号或需要修改端口号配置，执行如下操作。

 

1. 停止集群。

    ```shell
    $ yasboot cluster stop -c yashandb
    ```

2. 启动YASFS服务。

    ```shell
    $ yasfs -D /data/yashan/yasdb_data/ycs/ce-1-1&
    ```

3. 给目标节点配置服务端口号，命令中的节点名和端口号请按实际情况填写。

    ```shell
    $ ycsctl modify node yas2 serviceport=1688
    ```

4. 停止YASFS服务。

    ```shell
    $ yfscmd exec "shudown abort"
    ```

5. 启动集群。

    ```shell
    $ yasboot cluster start -c yashandb
    ```


### 步骤3：配置并启动VIP

1. 添加VIP配置信息。

    ```shell
    # 命令中的节点名、VIP地址和子网掩码请按实际情况填写
    $ ycsctl add vip -n host0001 --vip 192.168.1.62/24
    $ ycsctl add vip -n host0002 --vip 192.168.1.63/24
    ```

2. 依次登录集群中每台服务器启动VIP资源。

    ```shell
    $ ycsctl start vip
    ```

3. 检查VIP资源状态。

    ```shell
    $ ycsctl status
    ---------------------------------------------------------------------------------------------
    Self Host ID|Cluster Master ID|YasFS Master ID|YasDB Master ID|Active Host Count
    ---------------------------------------------------------------------------------------------
    1            1                 1               1               2
    ---------------------------------------------------------------------------------------------
    Host ID   |Target    |State     |YasFS     |YasDB     |VIP
    ---------------------------------------------------------------------------------------------
    1          online     online     online     online     host1.online
    2          online     online     online     online     host2.online

    $ ycsctl show config
        Cluster name: yashandb, config version: 6
        ……

        Network: 192.168.1.0/24
        Resource SCAN: disabled
        Resource vip: enabled
        ……

        Nodes in cluster:
        Node name: yas1, yascs/yasfs inter connect URL: 172.16.1.2:1788, Node ID: 1
            public service port: 1688
            yasdb instance name:yasdb-1-1, yasdb instance id:1
            VIP: 192.168.1.62/24/ens192, home node: yas1
        Node name: yas2, yascs/yasfs inter connect URL: 172.16.1.3:1788, Node ID: 2
            public service port: 1688
            yasdb instance name:yasdb-1-2, yasdb instance id:1
            VIP: 192.168.1.63/24/ens192, home node: yas2
    ```

## 修改VIP资源配置 

在进行集群环境运维时，例如修改网络配置、重新分配IP地址和端口等，可以根据实际需求更新VIP资源的配置信息。

> **Caution**:
>
> 在调整相应配置前，请确保上层业务已不依赖基于目标VIP及端口的连接信息，或集群有其他在线的VIP资源能提供连接服务。

### 更换节点服务端口 

如需更换节点服务端口，请执行以下操作：

 

1. 停止集群。

    ```shell
    $ yasboot cluster stop -c yashandb
    ```

2. 启动YASFS服务。

    ```shell
    $ yasfs -D /data/yashan/yasdb_data/ycs/ce-1-1&
    ```

3. 给目标节点配置服务端口号，命令中的节点名和端口号请按实际情况填写。

    ```shell
    $ ycsctl modify node yas2 serviceport=1688
    ```

4. 停止YASFS服务。

    ```shell
    $ yfscmd exec "shudown abort"
    ```

5. 启动集群。

    ```shell
    $ yasboot cluster start -c yashandb
    ```


### 更换VIP地址 

如需更换VIP地址，需先删除旧VIP再配置新VIP地址。

1. 以安装用户登录数据库安装服务器。


2. 删除目标节点的旧VIP。

    ```shell
    # 示例将强制停止节点yas1的VIP并删除配置信息

    $ ycsctl remove vip -n yas1 -f
    ```

3. 添加新的VIP资源配置信息并启动VIP资源。

    ```shell
    # 示例把192.168.1.71地址分配给节点yas1作为新的VIP地址
    $ ycsctl add vip -n yas1 192.168.1.71/24/ens192

    $ ycsctl start vip
    ```

3. 查看集群配置，确认修改结果。

    ```shell
    $ ycsctl show config
        Cluster name: yashandb, config version: 10
        ……

        Network: 192.168.1.0/24
        Resource SCAN: disabled
        Resource vip: enabled
        ……

        Nodes in cluster:
        Node name: yas1, yascs/yasfs inter connect URL: 172.16.1.2:1788, Node ID: 1
            public service port: 1688
            yasdb instance name:yasdb-1-1, yasdb instance id:1
            VIP: 192.168.1.71/24/ens192, home node: yas1
        ……
    ```

### 删除所有VIP资源配置 

删除VIP资源配置时，单次只能删除1个节点的配置。

> **Warn**:
>
> 删除VIP属于**危险操作**，请在删除VIP前确保VIP所在节点的数据库实例处于空闲状态没有运行业务。

1. 以安装用户登录数据库安装服务器。


2. 依次删除每个节点的VIP资源配置。

    ```shell
    $ ycsctl remove vip -n yas1 -f
    $ ycsctl remove vip -n yas2 -f
    ```

 

### 删除公网配置 

删除公网配置前，必须先删除所有VIP和SCAN配置才能删除公网。


1. 以安装用户登录数据库安装服务器。


2. 删除公网配置。

    ```shell
    $ ycsctl remove network
    ```



## 常见问题

### 添加VIP时提示“specified ip is reachable”该怎么处理？ 

该提示表示VIP分配的IP地址已被其他服务器占用，应按要求为规划并预留VIP地址。

### 添加VIP时提示“network interface xxx is invalid”该怎么处理？ 

该提示表示VIP关联的网卡状态异常或协议不支持。请确认网卡状态是否正常，以及网卡是否为以太网卡、是否支持ARP/NDP协议。

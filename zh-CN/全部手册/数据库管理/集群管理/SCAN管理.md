## 功能简介

单客户端访问名称（SCAN，Single Client Access Name）是一种智能化的连接管理机制，为共享集群提供统一访问入口。它通过虚拟IP技术和智能DNS解析，使客户端能够通过单一逻辑名称（即SCAN域名）访问后端集群服务，且能通过负载均衡算法动态分配连接请求。使用者无需感知具体的集群节点拓扑变化，不论是节点故障还是扩缩容均对使用者透明，简化客户端配置的同时也更大程度地保障了服务高可用。

在共享集群部署中，SCAN是由YCS管理的一种集群服务资源，其工作流程大致如下：

1. 规划SCAN域名和1 - 3个SCAN VIP，并根据规划信息在DNS服务器中配置DNS解析规则。

2. 为集群添加SCAN时，YCS从DNS服务器中获取SCAN VIP并进行管理。

    - 集群启动时，SCAN VIP会分配到集群的节点中，运行SCAN VIP的节点会启动一个SCAN监听，用于处理连接请求，并依据负载均衡规则将连接动态分配到数据库实例。

    - SCAN VIP及其监听器默认运行在不同节点，除非节点数与SCAN VIP个数不一致，则可能部分节点上运行0个或多个SCAN VIP及其监听器。

    - 当节点故障时，其上运行的SCAN VIP及其监听器会自动迁移至健康节点。

3. 客户端/驱动通过SCAN发起连接数据库的请求，DNS解析后得到所有SCAN VIP地址并随机连接其中1个SCAN VIP。SCAN监听接收到SCAN连接请求时，依据实时的负载信息将负载最低的数据库实例的连接串（如果开启了[VIP](./VIP管理)功能则返回`VIP:port`，否则返回LISTEN_ADDR）返回给客户端/驱动。客户端/驱动根据返回的结果与数据库实例建立连接，并完成后续业务。

SCAN的启停和高可用管理需要YCSRA进程在线以提供特权操作代理服务，请确保YCSRA进程在每个服务器上都已启动，否则请参考[ycsrootagent工具](../../工具手册/ycsrootagent)手动sudo拉起。为简化运维，建议在每台服务器上为该进程配置开机自启动，具体操作请查阅[配置开机自启动](../../安装和升级/安装部署/安装后初始环境/配置开机自启动)。

> **Note**: 
>
> 如需在运行着SCAN的集群服务器上重启网络，请先手动执行ycsctl stop scan停止该节点正在运行的SCAN，待网络重启完成后，再执行ycsctl start scan重新启动SCAN。


## 配置要求



- 服务器的网卡必须为以太网卡，且支持ARP/NDP协议。

- 必须先为集群配置公网信息（安装部署时yasboot package ce gen命令指定--public-network参数或安装完成后执行ycsctl add network命令指定）后才能使用SCAN。

- 需为集群规划1 - 3个SCAN VIP地址，且统一采用IPv4或IPv6。

- 配置SCAN后，通过SCAN域名连接数据库的驱动或客户端也应为配套的23.4.4.100及以上版本。



<span id="scan_configuration" name="scan_configuration" class="yaslink"></span>

## 为已有集群配置SCAN

用户在安装部署共享集群时未配置SCAN，或者从不支持（或未开启）SCAN的旧版本集群升级到支持SCAN的新版本集群，可以按照如下流程为集群配置SCAN资源。

### 前提条件

- 当前数据库版本为23.4.4.100及以上，或已升级到该版本及以上。

- 已规划符合配置要求的SCAN IP地址，并完成DNS配置，配置格式如下：

    ```bash
    # DNS记录
    scan.example.com IN A 192.168.1.100
    ……
    
    # 反向解析记录
    100.1.168.192.in-addr.arpa IN PTR scan.example.com
    ……
    ```



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



### 步骤2：配置并启动SCAN 

1. 添加SCAN配置信息。

    ```shell
    $ ycsctl add scan -scanname scan.example.com -p 1688
    ```
    此时，YCS会从DNS服务器的DNS解析规则配置中获取SCAN VIP地址。

2. 启动所有SCAN VIPs。

    ```shell
    $ ycsctl start scan
    ```

3. 检查SCAN配置。

    ```shell
    $ ycsctl show config
        Cluster name: yashandb, config version: 6
        ……

        Network: 192.168.1.0/24
        Resource SCAN: enabled
        SCAN name: scan.example.com, listening port: 1688
        SCAN VIP: 192.168.1.100, ordinal number: 1
        SCAN VIP: 192.168.1.101, ordinal number: 2
        SCAN VIP: 192.168.1.102, ordinal number: 3
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

4. 查看集群的拓扑状态。

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
    ---------------------------------------------------------------------------------------------
    SCAN VIP 1: host2.online   SCAN VIP 2: host1.online   SCAN VIP 3: host1.online
    ```

## 修改SCAN资源配置

用户在进行集群环境运维（如修改网络配置、重新分配IP地址和端口等）时，可以按照如下流程更新SCAN资源的配置信息。

> **Caution**:
>
> 在调整相应配置前，请确保上层业务暂时不依赖基于SCAN的连接信息。

### 更新SCAN配置 

如需更新SCAN域名、端口或SCAN VIP地址，需先删除旧SCAN资源再配置新SCAN资源。

 

1. 以安装用户登录数据库安装服务器。


2. 停止所有SCAN VIP。

    ```shell
    $ ycsctl stop scan
    ```

3. 删除现有SCAN配置。

    ```shell
    $ ycsctl remove scan
    ```


4. 如需更新SCAN VIP地址或SCAN域名，需在DNS服务器上更新DNS解析规则。

5. 添加新的SCAN配置信息。

    ```shell
    $ ycsctl add scan -scanname scan_new.example.com -p 1688
    ```

6. 重新启动所有SCAN VIP。

    ```shell
    $ ycsctl start scan
    ```

7. 查看集群配置，确认修改结果。

    ```shell
    $ ycsctl show config
        Cluster name: yashandb, config version: 6
        ……

        Network: 192.168.1.0/24
        Resource SCAN: enabled
        SCAN name: scan_new.example.com, listening port: 1688
        SCAN VIP: 192.168.1.100, ordinal number: 1
        SCAN VIP: 192.168.1.101, ordinal number: 2
        SCAN VIP: 192.168.1.102, ordinal number: 3
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

8. 查看集群的拓扑状态。

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
    ---------------------------------------------------------------------------------------------
    SCAN VIP 1: host2.online   SCAN VIP 2: host1.online   SCAN VIP 3: host1.online
    ```

### 手动迁移SCAN VIP 

SCAN VIP具备自动迁移能力，通常无需维护SCAN VIP与数据库实例节点的对应关系。

常见的自动迁移场景如下：

- 某个SCAN VIP的运行节点故障时会自动迁移至其他健康节点。

- 若SCAN VIP数量高于数据库实例节点数量，执行集群实例扩容时，会将额外的SCAN VIP自动迁移至新的实例节点。

手动迁移的步骤如下：

1. 以安装用户登录数据库安装服务器。


2. 查询SCAN VIP的序号（即ordinal number字段）。

    ```shell
    $ ycsctl show config
        Cluster name: yashandb, config version: 6
        ……

        Network: 192.168.1.0/24
        Resource SCAN: enabled
        SCAN name: scan.example.com, listening port: 1688
        SCAN VIP: 192.168.1.100, ordinal number: 1
        SCAN VIP: 192.168.1.101, ordinal number: 2
        SCAN VIP: 192.168.1.102, ordinal number: 3
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

3. 手动迁移目标SCAN VIP。
    
    ```shell
    # 指定节点迁移
    $ ycsctl relocate scan -scannumber 3 -node yas2

    # 自动迁移
    $ ycsctl relocate scan -scannumber 3
    ```

### 删除SCAN资源配置

 

1. 以安装用户登录数据库安装服务器。


2. 停止所有SCAN VIP。

    ```shell
    $ ycsctl stop scan
    ```

3. 删除现有SCAN配置。

    ```shell
    $ ycsctl remove scan
    ```
 


 

### 删除公网配置 

删除公网配置前，必须先删除所有VIP和SCAN配置才能删除公网。


1. 以安装用户登录数据库安装服务器。


2. 删除公网配置。

    ```shell
    $ ycsctl remove network
    ```

 

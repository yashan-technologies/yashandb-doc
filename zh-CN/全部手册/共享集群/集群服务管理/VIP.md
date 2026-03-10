## 功能简介

VIP（Virtual IP）是由YCS管理的一种节点服务资源，数据库实例监听VIP并提供服务。VIP与实例监听地址（LISTEN_ADDR）的差异在于，VIP可以在故障实例和正常实例之间迁移使得实例级故障对用户透明。

当某个集群节点发生宕机等故障时，YCS首先会进行投票仲裁将故障节点驱逐出集群，再按照高可用和均匀分布的原则将故障节点的VIP资源重新分布到正常节点。正常节点接管故障的VIP后，会在该节点运行的服务器上重新启动该VIP并在网络层将连接该VIP的请求和流量重定向到当前服务器，同时，该节点管理的数据库实例也会动态地添加对该VIP的监听。经过上述故障转移流程，故障实例的VIP会快速恢复连接可用，此次集群故障也会在最大程度上对客户的业务透明。

VIP的启停和高可用管理需要YCSRA进程在线以提供特权操作代理服务，请确保YCSRA进程在每个服务器上都已启动，否则请参考[ycsrootagent工具](../../工具手册/ycsrootagent)手动sudo拉起。为简化运维，建议在每台服务器上为该进程配置开机自启动，具体操作请查阅[配置开机自启动](../../安装和升级/安装部署/安装后初始环境/配置开机自启动)。

> **Note**: 
>
> 如需在运行着VIP的集群服务器上重启网络，请先手动执行ycsctl stop vip停止该节点正在运行的VIP，待网络重启完成后，再执行ycsctl start vip重新启动VIP。

## 配置要求


- 同一共享集群中的所有节点必须采用统一的VIP配置策略（主备集群之间VIP配置策略可以不同）：

    - 节点统一配置VIP或不配置。

    - VIP统一采用IPv4或IPv6。

- VIP应规划为预留的空闲IP地址，共享集群完成部署前，该地址不存在也无法ping通。

- VIP必须与实例监听地址（LISTEN_ADDR）处于相同子网、相同网卡。

- 同一集群中所有服务器用于配置VIP的网卡的名称必须相同。

<span id="vip_configuration" name="vip_configuration" class="yaslink"></span>

## 开启VIP

用户在安装部署共享集群时未配置VIP，或者从不支持（或未开启）VIP的旧版本集群升级到支持VIP的新版本集群，可以按照如下流程为集群配置VIP资源。

### 前提条件

- 当前数据库版本为23.4.2.100及以上，或已升级到该版本及以上。

- 已规划符合VIP配置要求的IP地址。

<span id="serviceport_configuration" name="serviceport_configuration" class="yaslink"></span>

### 步骤1：配置节点服务端口号

1. 以安装用户登录数据库安装服务器。

2. 检查确认每个集群节点都已配置服务端口号（service port）。
```shell
# 查看集群YCR配置信息
$ ycsctl show config
    Cluster name: yashandb, config version: 6
    Cluster id: 3eedfe29261c713ea258a22f181c5218
    Network timeout: 30s
    Disk heartbeat keep alive: 60s
    Fence type: SCSI I/O Fencing
    Default resource yasfs: enabled
    Resource vip: disabled
    Shell in cluster:
    Start shell:   start.sh
    Stop shell:    stop.sh
    Monitor shell: monitor.sh
    Nodes in cluster:
    Node name: yas1, yascs/yasfs inter connect URL: 192.168.7.133:1788, Node ID: 1
                                            # 节点yas1未配置服务端口号
        yasdb instance name:yasdb-1-1, yasdb instance id:1
    Node name: yas2, yascs/yasfs inter connect URL: 192.168.7.134:1788, Node ID: 2
        public service port: 1601           # 节点yas2已配置服务端口号为1601
        yasdb instance name:yasdb-1-2, yasdb instance id:1
```

3. 若有节点未配置服务端口号或需要修改端口号配置时，执行如下操作。
```shell
# 停止集群
$ yasboot cluster stop -c yashandb

# 启动YASFS服务
$ yasfs -D /data/yashan/yasdb_data/ycs/ce-1-1&

# 给每个节点配置服务端口号，命令中的节点名和端口号请按实际情况填写
$ ycsctl modify node yas1 serviceport=1688
$ ycsctl modify node yas2 serviceport=1688

# 停止YASFS服务
$ yfscmd exec "shudown abort"

# 启动集群
$ yasboot cluster start -c yashandb
```

### 步骤2：配置并启动VIP

1. 添加VIP配置信息。
```shell
# 命令中的节点名、VIP地址、子网掩码和网卡名请按实际情况填写
$ ycsctl add vip -n yas1 192.168.1.62/24/ens192
$ ycsctl add vip -n yas2 192.168.1.63/24/ens192
```

2. 依次登录集群中每台服务器启动VIP资源。
```shell
$ ycsctl start vip
```

3. 检查VIP资源状态
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
```

## 修改VIP

用户在进行集群环境运维（如修改网络配置、重新分配IP地址和端口等）时，可以按照如下流程更新VIP资源的配置信息。

### 步骤1：删除旧的VIP资源配置信息

旧的VIP资源被删除前，请确保上层业务已不依赖该VIP提供的连接服务，或者集群有其他在线的VIP资源能提供连接服务。

```shell
# 示例将强制停止节点yas1的VIP并删除配置信息
$ ycsctl remove vip -n yas1 -f
```

### 步骤2：配置并启动新的VIP资源

1. 确认VIP监听的端口号是否需要变更，若是，执行以下步骤：
```shell
# 停止集群
$ yasboot cluster stop -c yashandb

# 启动YASFS服务
$ yasfs -D /data/yashan/yasdb_data/ycs/ce-1-1&

# 修改节点yas1的服务端口号，命令中的节点名和端口号请按实际情况填写
$ ycsctl modify node yas1 serviceport=1689

# 停止YASFS服务
$ yfscmd exec "shudown abort"

# 启动集群
$ yasboot cluster start -c yashandb
```

2. 添加新的VIP资源配置信息并启动VIP资源
```shell
# 示例把192.168.1.121地址分配给节点yas1作为新的VIP地址
$ ycsctl add vip -n yas1 192.168.1.121/24/ens192

$ ycsctl start vip

# 查看集群topo
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
```

## 常见问题

### 添加VIP失败

- 错误码YAS-00432：VIP关联的网卡状态异常或协议不支持。请确认网卡状态是否正常，以及网卡是否以太网卡、是否支持ARP/NDP协议。

- 错误码YAS-05767：VIP配置信息不合法（如格式错误、IP地址不合法、子网掩码不合法等）。

- 错误信息“specified ip is reachable”：VIP分配的IP地址已被其他服务器占用。

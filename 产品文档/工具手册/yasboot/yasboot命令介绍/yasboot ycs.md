## ycs status

本命令可以查看对应节点的YCS中各节点的状态信息（结果等同于ycsctl status命令，显示的节点数目等于部署的节点数目）。

| 选项            | 含义                                                         |
| --------------- | ------------------------------------------------------------ |
| *-c, --cluster* | YashanDB的集群名（必传参数）                                 |
| *-n, --node-id* | 目标节点的ID（例如`1-2`，可通过yasboot cluster status命令查看数据库信息取`nodeid`冒号前的数字串）（必传参数） |

示例

```shell
$ yasboot ycs status -c yashandb -n 1-1
```

**命令结果**

```text

---------------------------------------------------------------------------------------------
Self Host ID|Cluster Master ID|YasFS Master ID|YasDB Master ID|Active Host Count
---------------------------------------------------------------------------------------------
1            1                 1               1               3
---------------------------------------------------------------------------------------------
Host ID   |Target    |State     |YasFS     |YasDB
---------------------------------------------------------------------------------------------
1          online     online     online     online
2          online     online     online     online
3          online     online     online     online

```

##  ycs show

本命令可以查看对应节点的YCS中配置信息（结果等同于ycsctl show config命令）。

| 选项            | 含义                                                         |
| --------------- | ------------------------------------------------------------ |
| *-c, --cluster* | YashanDB的集群名（必传参数）                                 |
| *-n, --node-id* | 目标节点的ID（例如`1-2`，可通过yasboot cluster status命令查看数据库信息取`nodeid`冒号前的数字串）（必传参数） |

示例

```shell
$ yasboot ycs show -c yashandb -n 1-1
```

**命令结果**

```text

    Cluster name: yashandb, config version: 6
    Network timeout: 30s
    Disk heartbeat keep alive: 30s
    Default resource yasfs: enabled
    Shell in cluster:
      Start shell:   start.sh
      Stop shell:    stop.sh
      Monitor shell: monitor.sh
    Nodes in cluster:
      Node name: yas1, yascs/yasfs inter connect URL: 192.168.1.2:1788, Node ID: 1
        yasdb instance name:yasdb, yasdb instance id:1
      Node name: yas2, yascs/yasfs inter connect URL: 192.168.1.2:1789, Node ID: 2
        yasdb instance name:yasdb, yasdb instance id:1
      Node name: yas3, yascs/yasfs inter connect URL: 192.168.1.2:1790, Node ID: 3
        yasdb instance name:yasdb, yasdb instance id:1

```

## ycs instance start

本命令用于启动集群节点对应的实例 （结果等同于`ycsctl start instance`命令，和节点`yascs`进程上一次启动的实例状态相同）。

| 选项            | 含义                                                         |
| --------------- | ------------------------------------------------------------ |
| *-c, --cluster* | YashanDB的集群名（必传参数）                                 |
| *-n, --node-id* | 目标节点的ID（例如`1-2`，可通过yasboot cluster status命令查看数据库信息取`nodeid`冒号前的数字串）（必传参数） |
| *-w, --nowait*  | 运行后不等待执行命令结果                                     |
| *-d, --child*   | 展示任务以及子任务信息                                       |
| *--disable*     | 屏蔽任务进度条展示                                           |
| *-u, --username*     | 指定数据库用户，不指定则默认使用sys用户           |
| *-p, --password* | 数据库用户的密码<br/>若使用sys用户且已开启[操作系统认证](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证)（安装后默认开启）则无需指定密码                         |
| *--wait-timeout* | 命令执行超时时间（隐藏参数）                  |

示例

```shell
$ yasboot ycs instance start -c yashandb -n 1-1
```

## ycs instance stop

本命令用于停止集群节点对应的实例 （结果等同于`ycsctl stop instance`命令）。

| 选项            | 含义                                                         |
| --------------- | ------------------------------------------------------------ |
| *-c, --cluster* | YashanDB的集群名（必传参数）                                 |
| *-n, --node-id* | 目标节点的ID（例如`1-2`，可通过yasboot cluster status命令查看数据库信息取`nodeid`冒号前的数字串）（必传参数） |
| *-f, --force*   | 是否强制停止实例，默认不强制                                 |
| *-w, --nowait*  | 运行后不等待执行命令结果                                     |
| *-d, --child*   | 展示任务以及子任务信息                                       |
| *--disable*     | 屏蔽任务进度条展示                                           |
| *--wait-timeout* | 命令执行超时时间（隐藏参数）                  |

示例

```shell
$ yasboot ycs instance stop -c yashandb -n 1-1
```

##  ycs config

本命令可以查看对应节点的YCS配置信息。

| 选项            | 含义                                                        |
| --------------- | ----------------------------------------------------------- |
| *-c, --cluster* | YashanDB的集群名（必传参数）                                |
| *-n, --node-id* | 目标节点的ID（例如`1-2`，可通过yasboot cluster status命令查看数据库信息取`nodeid`冒号前的数字串）（必传参数） |
| *-q, --query*     | 查询组配置中单个或多个参数（可以模糊匹配）            |
| *-a, -all*        | 查询所有参数     |

示例

```shell
$ ./bin/yasboot ycs config show -c yashandb -n 1-1
```

**命令结果**

```text

+--------------------------------------------------+
| parameters | current_value                       |
+--------------------------------------------------+
| _HOST_NAME | yas1                                |
+------------+-------------------------------------+

```

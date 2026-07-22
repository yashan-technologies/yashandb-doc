## whitelist on

本命令用于打开白名单。

> **Warn**:
>
> 打开白名单时，如果当前的IP为空，则会写入当前节点所在的IP。

|  选项| 含义|
| ---------------- | ---------------------------------------------------------- |
| *-c, --cluster*  | YashanDB的集群名                                           |
| *-g, --group-id* | 目标组的ID（例如`1`，可通过yasboot cluster status命令查看数据库信息取`nodeid`中横线前的数字）<br/>与--node-id选项互斥        |
| *-n, --node-id*  | 目标节点的ID（例如`1-2`，可通过yasboot cluster status命令查看数据库信息取`nodeid`冒号前的数字串）<br/>与--group-id选项互斥 |
| *-d, --child*    | 展示任务以及子任务信息                                     |
| *--disable*      | 屏蔽任务进度条展示                                         |
| *-w, --nowait*   | 运行后不等待执行命令结果                                   |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$ yasboot whitelist on -c yashandb -g 1
```

## whitelist off

本命令用于关闭白名单。

|  选项| 含义|
| ---------------- | ---------------------------------------------------------- |
| *-c, --cluster*  | YashanDB的集群名                                           |
| *-g, --group-id* | 目标组的ID（例如`1`，可通过yasboot cluster status命令查看数据库信息取`nodeid`中横线前的数字）<br/>与--node-id选项互斥       |
| *-n, --node-id*  | 目标节点的ID（例如`1-2`，可通过yasboot cluster status命令查看数据库信息取`nodeid`冒号前的数字串）<br/>与--group-id选项互斥 |
| *-d, --child*    | 展示任务以及子任务信息                                     |
| *--disable*      | 屏蔽任务进度条展示                                         |
| *-w, --nowait*   | 运行后不等待执行命令结果                                   |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$ yasboot whitelist off -c yashandb -g 1
```

## whitelist add

本命令用于添加白名单ip。

|  选项| 含义|
| ---------------- | ---------------------------------------------------------- |
| *-c, --cluster*  | YashanDB的集群名                                           |
| *-g, --group-id* | 目标组的ID（例如`1`，可通过yasboot cluster status命令查看数据库信息取`nodeid`中横线前的数字）<br/>与--node-id选项互斥       |
| *-n, --node-id*  | 目标节点的ID（例如`1-2`，可通过yasboot cluster status命令查看数据库信息取`nodeid`冒号前的数字串）<br/>与--group-id选项互斥 |
| *--ip*           | 添加的ip，支持多个，使用逗号分隔                           |
| *-r, --replace*  | 用指定的ip替换当前白名单的所有ip                           |
| *-d, --child*    | 展示任务以及子任务信息                                     |
| *--disable*      | 屏蔽任务进度条展示                                         |
| *-w, --nowait*   | 运行后不等待执行命令结果                                   |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
# IPv4
$ yasboot whitelist add -c yashandb -n 1-1 --ip 192.168.1.1,192.168.1.2

# IPv6，不需要带中括号
$ yasboot whitelist add -c yashandb -n 1-1 --ip fc00:7::126,fe80::20c:29ff:fea5:66c2%ens33
```

## whitelist remove

本命令用于删除白名单ip。

|  选项| 含义|
| ---------------- | ---------------------------------------------------------- |
| *-c, --cluster*  | YashanDB的集群名                                           |
| *-g, --group-id* | 目标组的ID（例如`1`，可通过yasboot cluster status命令查看数据库信息取`nodeid`中横线前的数字）<br/>与--node-id选项互斥       |
| *-n, --node-id*  | 目标节点的ID（例如`1-2`，可通过yasboot cluster status命令查看数据库信息取`nodeid`冒号前的数字串）<br/>与--group-id选项互斥 |
| *--ip*           | 删除的ip，支持多个，使用逗号分隔                           |
| *-a, --all*      | 清空所有的ip，优先级高于--ip                               |
| *-d, --child*    | 展示任务以及子任务信息                                     |
| *--disable*      | 屏蔽任务进度条展示                                         |
| *-w, --nowait*   | 运行后不等待执行命令结果                                   |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$ yasboot whitelist remove -c yashandb -n 1-1 -a
```

## whitelist show

本命令用于查看白名单的信息详情。

|  选项| 含义|
| ---------------- | ---------------------------------------------------------- |
| *-c, --cluster*  | YashanDB的集群名                                           |
| *-g, --group-id* | 目标组的ID（例如`1`，可通过yasboot cluster status命令查看数据库信息取`nodeid`中横线前的数字）<br/>与--node-id选项互斥       |
| *-n, --node-id*  | 目标节点的ID（例如`1-2`，可通过yasboot cluster status命令查看数据库信息取`nodeid`冒号前的数字串）<br/>与--group-id选项互斥 |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$ yasboot whitelist show -c yashandb -g 2
node 2-1:
	TCP.VALIDNODE_CHECKING = YES
	TCP.INVITED_NODES = 127.0.0.1

node 2-2:
	TCP.VALIDNODE_CHECKING = YES
	TCP.INVITED_NODES = 127.0.0.1
```

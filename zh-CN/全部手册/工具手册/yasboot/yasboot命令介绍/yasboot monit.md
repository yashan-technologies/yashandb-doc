## monit start

本命令用于启动所有服务器上的monit进程。

|  选项| 含义|
| --------------- | ------------------------ |
| *-c, --cluster* | YashanDB的集群名（必传参数）         |
| *-w, --nowait*  | 运行后不等待执行命令结果 |
| *-d, --child*   | 展示任务以及子任务信息   |
| *--disable*     | 屏蔽任务进度条展示       |

示例

```shell
$ yasboot monit start -c yashandb -d
```

## monit stop

本命令用于停止所有服务器上的monit进程。

|  选项| 含义|
| --------------- | ------------------------ |
| *-c, --cluster* | YashanDB的集群名（必传参数）         |
| *-w, --nowait*  | 运行后不等待执行命令结果 |
| *-d, --child*   | 展示任务以及子任务信息   |
| *--disable*     | 屏蔽任务进度条展示       |

示例

```shell
$ yasboot monit stop -c yashandb -d
```

## monit restart

本命令用于重启所有服务器上的monit进程。

|  选项| 含义|
| --------------- | ------------------------ |
| *-c, --cluster* | YashanDB的集群名（必传参数）         |
| *-w, --nowait*  | 运行后不等待执行命令结果 |
| *-d, --child*   | 展示任务以及子任务信息   |
| *--disable*     | 屏蔽任务进度条展示       |

示例

```shell
$ yasboot monit restart -c yashandb -d
```

## monit status

本命令用于查看monit的监控状态详细信息。

|  选项| 含义|
| --------------- | ---------------------------------- |
| *-c, --cluster* | YashanDB的集群名（必传参数）                   |
| *-n,--node-id*  | 目标节点的ID（例如`1-2`，可通过yasboot cluster status命令查看数据库信息取`nodeid`冒号前的数字串） |

示例

```shell
$ yasboot monit status -c yashandb
$ yasboot monit status -c yashandb -n 1-1
```

## monit summary

本命令用于查看monit的监控状态简略信息。

|  选项| 含义|
| --------------- | ---------------------------------- |
| *-c, --cluster* | YashanDB的集群名（必传参数）                   |
| *-n,--node-id*  | 目标节点的ID（例如`1-2`，可通过yasboot cluster status命令查看数据库信息取`nodeid`冒号前的数字串） |

示例

```shell
$ yasboot monit summary -c yashandb
$ yasboot monit summary -c yashandb -n 1-1
```

## monit watch

本命令用于开启某个节点的进程监控。

|  选项| 含义|
| --------------- | ---------------------------------- |
| *-c, --cluster* | YashanDB的集群名（必传参数）                   |
| *-n,--node-id*  | 目标节点的ID（例如`1-2`，可通过yasboot cluster status命令查看数据库信息取`nodeid`冒号前的数字串）（必传参数） |
| *-w, --nowait*  | 运行后不等待执行命令结果           |
| *-d, --child*   | 展示任务以及子任务信息             |
| *--disable*     | 屏蔽任务进度条展示                 |

示例

```shell
$ yasboot monit watch -c yashandb -n 1-1 -d
```

## monit unwatch

本命令用于关闭某个节点的进程监控。

|  选项| 含义|
| --------------- | ---------------------------------- |
| *-c, --cluster* | YashanDB的集群名（必传参数）                   |
| *-n,--node-id*  | 目标节点的ID（例如`1-2`，可通过yasboot cluster status命令查看数据库信息取`nodeid`冒号前的数字串）（必传参数） |
| *-w, --nowait*  | 运行后不等待执行命令结果           |
| *-d, --child*   | 展示任务以及子任务信息             |
| *--disable*     | 屏蔽任务进度条展示                 |

示例

```shell
$ yasboot monit unwatch -c yashandb -n 1-1 -d
```

## monit reload

本命令用于重新加载所有服务器上的monit的配置。

|  选项| 含义|
| --------------- | ------------------------ |
| *-c, --cluster* | YashanDB的集群名（必传参数）         |
| *-w, --nowait*  | 运行后不等待执行命令结果 |
| *-d, --child*   | 展示任务以及子任务信息   |
| *--disable*     | 屏蔽任务进度条展示       |

示例

```shell
$ yasboot monit reload -c yashandb -d
```

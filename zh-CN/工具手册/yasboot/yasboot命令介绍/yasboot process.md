## process yasom status

本命令用于查看yasom进程状态。

|  选项| 含义|
| --------------- | :--------------------------------------------------- |
| *-c, --cluster* | YashanDB的集群名（必传参数）                                     |
| *-t, --toml*    | 通过服务器配置文件`hosts.toml`查看所有服务器上的yasom状态 |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$ yasboot process yasom status -c yashandb
$ yasboot process yasom status -c yashandb -t hosts.toml
```

## process yasom stop

本命令用于停止本机的yasom进程。

|  选项| 含义|
| --------------- |:------------------------------|
| *-c, --cluster* | YashanDB的集群名（必传参数）            |
| *-t, --toml*    | 通过服务器配置文件`hosts.toml`停止所有服务器上的yasom  |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$ yasboot process yasom stop -c yashandb
$ yasboot process yasom stop -c yashandb -t hosts.toml
```

## process yasom start

本命令用于启动yasom进程。

|  选项| 含义|
| --------------- | :----------------------------------------------- |
| *-c, --cluster* | YashanDB的集群名（必传参数）                                 |
| *-t, --toml*    | 通过服务器配置文件`hosts.toml`启动所有服务器上的yasom |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$ yasboot process yasom start -c yashandb
$ yasboot process yasom start -c yashandb -t hosts.toml
```

## process yasom restart

本命令用于重启yasom进程。

|  选项| 含义|
| --------------- | :----------------------------------------------- |
| *-c, --cluster* | YashanDB的集群名（必传参数）                                 |
| *-t, --toml*    | 通过服务器配置文件`hosts.toml`重启所有服务器上的yasom |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$ yasboot process yasom restart -c yashandb
$ yasboot process yasom restart -c yashandb -t hosts.toml
```

## process yasom recover

本命令用于通过备份集在当前服务器上恢复出一个yasom进程。

|  选项| 含义|
| ---------------- | ------------------------------------------------------------ |
| *-c, --cluster*  | YashanDB的集群名（必传参数）                                 |
| *-m, --meta*     | 备份集文件的路径，默认在安装路径/om/{cluster}/data/backup。默认选择最新的备份集 |
| *-l,--listen*    | yasom进程的监听端口                                          |
| *-f,--force*     | 跳过确认，直接恢复                                           |
| *--role*         | 恢复的yasom的角色，可选[primary,secondary]，默认为secondary  |
| *--force-create* | 强制使用旧的备份集恢复yasom，不推荐使用                      |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
# 在一台没有yasom的服务器上恢复出一个secondary yasom
$ yasboot process yasom recover -c yashandb -l 192.168.1.3:1675

# primary yasom宕机，在一台没有yasom的服务器上恢复出一个primary yasom
$ yasboot process yasom recover -c yashandb -l 192.168.1.3:1675 --role primary

# primary yasom 宕机，在一台已有secondary yasom的服务器上执行升主
$ yasboot process yasom recover -c yashandb --role primary
```

## process yasom clean

本命令用于清理当前服务器上的yasom进程。

|  选项| 含义|
| --------------- | ---------------------------- |
| *-c, --cluster* | YashanDB的集群名（必传参数） |
| *-f,--force*    | 跳过确认，直接清理           |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$ yasboot process yasom clean -c yashandb
```

## process yasom sync

本命令用于同步所有服务器的yasom配置，主要用于解决执行恢复yasom和清理yasom命令时由网络原因引起的数据不一致问题。

|  选项| 含义|
| --------------- | ---------------------------- |
| *-c, --cluster* | YashanDB的集群名（必传参数） |
| *-f,--force*    | 跳过确认，直接同步           |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$ yasboot process yasom sync -c yashandb
```

## process yasom repair on|off

本命令用于配置当前yasom的自修复功能。

开启自修复功能后，数据库集群正常但yasom出现多主现象时，会进行自动修复，保留1个主yasom并将其余的yasom自动降备。被降备的yasom会将自身的元数据备份至$YASDB_HOME/om/{集群名称}/data/repair/。

建议所有yasom的自修复配置保持一致，具体配置操作请查阅[yasom配置](../管理yasom/管理主备yasom.md)。

若自修复后数据库集群信息不符合预期，可使用yasboot cluster load命令重新加载信息。

使用自修复需遵循如下规则：

- yasom仲裁（yasboot election）与自修复互斥。

- 需开启[操作系统认证](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证.md)（遵循标准安装步骤时默认已开启）才能正常使用自修复功能。

|  选项| 含义|
|-----------------| ---------------------------- |
| *-c, --cluster* | YashanDB的集群名（必传参数）        |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
# 开启自修复
$ yasboot process yasom repair on -c yashandb

# 关闭自修复
$ yasboot process yasom repair off -c yashandb
```

## process yasom demote

本命令用于将yasom自身进行降备。

|  选项| 含义|
|-----------------| ---------------------------- |
| *-c, --cluster* | YashanDB的集群名（必传参数）        |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$  yasboot process yasom demote -c yashandb
```

## process yasagent status

本命令用于查看yasagent的运行状态。

|  选项| 含义|
| --------------- | :----------------------------------------------------------- |
| *-c, --cluster* | YashanDB的集群名（必传参数）                                             |
| *-t, --toml*    | 安装当前数据库所生成的服务器配置文件hosts.toml，无实际作用      |
| *--hostid*      | 目标服务器的hostid，必须指定为hosts.toml文件中记录的hostid<br>用于查询目标服务器上yasagent的运行状态 |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
# 查看所有节点上的yasagent的运行状态
$ yasboot process yasagent status -c yashandb

# 查看host0002服务器上的yasagent的运行状态
$ yasboot process yasagent status -c yashandb --hostid host0002
```

## process yasagent stop

本命令用于停止运行中的yasagent进程，支持停止其它服务器的yasagent进程。

|  选项| 含义|
| --------------- | :----------------------------------------------------------- |
| *-c, --cluster* | YashanDB的集群名（必传参数）                                             |
| *-t, --toml*    | 安装当前数据库所生成的服务器配置文件hosts.toml<br/>用于停止当前数据库所有节点上的yasagent      |
| *--hostid*      | 目标服务器的hostid，必须与`-t`参数配合使用且需指定为hosts.toml文件中记录的hostid<br>用于停止目标服务器上的yasagent |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
# 停止当前服务器上的yasagent
$ yasboot process yasagent stop -c yashandb

# 停止所有节点上的yasagent
$ yasboot process yasagent stop -c yashandb -t hosts.toml

# 停止host0002服务器上的yasagent
$ yasboot process yasagent stop -c yashandb -t hosts.toml --hostid host0002
```

## process yasagent start

本命令用于启动服务器的yasagent进程，支持启动其它服务器的yasagent进程。

|  选项| 含义|
| --------------- | :----------------------------------------------------------- |
| *-c, --cluster* | YashanDB的集群名（必传参数）                                             |
| *-t, --toml*    | 安装当前数据库所生成的服务器配置文件hosts.toml<br/>用于启动当前数据库所有节点上的yasagent      |
| *--hostid*      | 目标服务器的hostid，必须与`-t`参数配合使用且需指定为hosts.toml文件中记录的hostid<br>用于启动目标服务器上的yasagent |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
# 启动当前服务器上的yasagent
$ yasboot process yasagent start -c yashandb

# 启动所有节点上的yasagent
$ yasboot process yasagent start -c yashandb -t hosts.toml

# 启动host0002服务器上的yasagent
$ yasboot process yasagent start -c yashandb -t hosts.toml --hostid host0002
```

## process yasagent restart

本命令用于重启服务器的yasagent进程，支持重启其它服务器的yasagent进程。

|  选项| 含义|
| --------------- | :----------------------------------------------------------- |
| *-c, --cluster* | YashanDB的集群名（必传参数）                                             |
| *-t, --toml*    | 安装当前数据库所生成的服务器配置文件hosts.toml<br/>用于重启当前数据库所有节点上的yasagent      |
| *--hostid*      | 目标服务器的hostid，必须与`-t`参数配合使用且需指定为hosts.toml文件中记录的hostid<br>用于重启目标服务器上的yasagent |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
# 重启当前服务器上的yasagent
$ yasboot process yasagent start -c yashandb

# 重启所有节点上的yasagent
$ yasboot process yasagent stop -c yashandb -t hosts.toml

# 重启host0002服务器上的yasagent
$ yasboot process yasagent start -c yashandb -t hosts.toml --hostid host0002
```

## process yasdb status

本命令用于查看本机yasdb的进程状态。

|  选项| 含义|
| --------------- | :---------------------------------------------------------- |
| *-c, --cluster* | 需要查看的yasdb进程状态对应的集群名，仅限于部署于本机的集群（必传参数） |
| *-u, --username*     | 指定数据库用户，不指定则默认使用sys用户           |
| *-p, --password*  | 数据库用户的密码<br/>若使用sys用户且已开启[操作系统认证](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证.md)（安装后默认开启）则无需指定密码   |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$ yasboot process yasdb status -c yashandb
```

## process yasdb stop

本命令用于停止本机的yasdb进程。

|  选项| 含义|
| --------------- | :------------------------------------------------------ |
| *-c, --cluster* | YashanDB的集群名（必传参数）                                        |
| *-n, --node-id* | 停止本机上的单个节点，需指定目标节点的ID（例如`1-2`，可通过yasboot cluster status命令查看数据库信息取`nodeid`冒号前的数字串） |
| *-f, --force*   | 强制停止节点，默认为非强制                              |
| *-s, --stop-mode*     | 关库方式，可选[normal&#124;immediate&#124;abort]，默认为immediate（共享集群/分布式集群部署暂时无法使用）   |
| *-u, --username*     | 指定数据库用户（不指定则默认使用sys用户）           |
| *-p, --password* | 数据库用户对应的密码                         |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
# 停止本机的yashandb集群下的所有节点
$ yasboot process yasdb stop -c yashandb

# 停止本机单个节点
$ yasboot process yasdb stop -c yashandb --node-id 2-1 -f
```

## process yasdb start

本命令用于启动本机的yasdb进程。

|  选项| 含义|
| ------------------ | :------------------------------------------------------ |
| *-c, --cluster*    | YashanDB的集群名（必传参数）                                        |
| *-n, --node-id*    | 启动本机上的单个节点，需指定目标节点的ID（例如`1-2`，可通过yasboot cluster status命令查看数据库信息取`nodeid`冒号前的数字串） |
| *-m, --start-mode* | 启动方式，可选[mount&#124;nomount&#124;open]，默认为open          |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
# 启动本机集群yashandb下的所有节点
$ yasboot process yasdb start -c yashandb

# 启动本机集群的单个节点
$ yasboot process yasdb start -c yashandb --node-id 2-1 --start-mode open
```

## process yasdb restart

本命令用于重启本机的yasdb进程。

|  选项| 含义|
| ------------------ | :------------------------------------------------------ |
| *-c, --cluster*    | YashanDB的集群名（必传参数）                                        |
| *-n, --node-id*    | 重启本机上的单个节点，需指定目标节点的ID（例如`1-2`，可通过yasboot cluster status命令查看数据库信息取`nodeid`冒号前的数字串） |
| *-m, --start-mode* | 节点的启动模式，默认为nomount                           |
| *-s, --stop-mode*     | 关库方式，可选[normal&#124;immediate&#124;abort]，默认为immediate（共享集群/分布式集群部署暂时无法使用）   |
| *-f, --force*      | 强制停止节点，默认为非强制                              |
| *-u, --username*     | 指定数据库用户，不指定则默认使用sys用户           |
| *-p, --password*  | 数据库用户的密码<br/>若使用sys用户且已开启[操作系统认证](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证.md)（安装后默认开启）则无需指定密码   |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
# 重启本机集群yashandb下的所有节点
$ yasboot process yasdb restart -c yashandb

# 重启本机yashandb集群的单个节点
$ yasboot process yasdb restart -c yashandb --node-id 2-1 --start-mode open
```

## process monit status

本命令用于查看monit进程状态。

|  选项| 含义|
| --------------- | :--------------- |
| *-c, --cluster* | YashanDB的集群名（必传参数） |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$ yasboot process monit status -c yashandb
```

## process monit stop

本命令用于停止monit进程。

|  选项| 含义|
| --------------- | :--------------- |
| *-c, --cluster* | YashanDB的集群名（必传参数） |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$ yasboot process monit stop -c yashandb
```

## process monit start

本命令用于启动monit进程。

|  选项| 含义|
| --------------- | :--------------- |
| *-c, --cluster* | YashanDB的集群名（必传参数） |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$ yasboot process monit start -c yashandb
```

## process monit restart

本命令用于重启monit进程。

|  选项| 含义|
| --------------- | :--------------- |
| *-c, --cluster* | YashanDB的集群名（必传参数） |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$ yasboot process monit restart -c yashandb
```

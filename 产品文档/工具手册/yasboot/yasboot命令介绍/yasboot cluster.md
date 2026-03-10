## cluster deploy
<span id="deploy" name="deploy" class="yaslink"></span>

本命令用于按照配置文件在所有服务器上部署和初始化YashanDB。

| 选项                 | 含义                                     |
| -------------------- | ---------------------------------------- |
| *-w, --nowait*       | 运行后不等待执行命令结果                 |
| *-t, --toml*         | 部署数据库的toml文件路径（必传参数） |
| *-d, --child*        | 展示任务以及子任务信息                   |
| *--disable*          | 屏蔽任务进度条展示                       |
| *--dump-uuid*        | 写入任务uuid文件到指定文件夹             |
| *-p, --sys-password* | 设置数据库sys用户的密码，要求如下：<br/>* 密码长度为8 - 64位<br/>* 密码中不能包含对应的数据库用户名称<br/>* 密码必须同时包含数字、字母和特殊字符<br/>* OS命令相关的特殊字符（例如`@`、`/`、`.`、`!`、`$`、`'`等）需进行转义                            |
| *--yfs-force-create* | YFS强制创建diskgroup（仅适用共享集群）   |
| *--recommend-force*  | 跳过内存超配的校验                       |
| *--wait-timeout*     | 命令执行超时时间（隐藏参数）                  |
| *--ignore-hostname*  | 忽略服务器名（隐藏参数）                |

> **Note**：
>
> 指定--ignore-hostname时，yasom将忽略服务器名称直接生成默认字符串作为集群节点名进行部署，**不推荐使用**，以服务器名称作为集群节点名更便于管理。

示例

```shell
$ yasboot cluster deploy -t yashandb.toml -p sys_password
```

## cluster status

本命令用于查询所有服务器上的YashanDB进程状态。

| 选项            | 含义                             |
| --------------- | -------------------------------- |
| *-c, --cluster* | YashanDB的集群名（必传参数）     |
| *-d, --detail*  | 展示更多细节                     |
| *-b, --by*      | 以host或组为索引展示，默认为host |
| *-u, --username*     | 指定数据库用户，不指定则默认使用sys用户           |
| *-p, --password* | 数据库用户的密码<br/>若使用sys用户且已开启[操作系统认证](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证)（安装后默认开启）则无需指定密码                         |

示例

```shell
$ yasboot cluster status -c yashandb -d
$ yasboot cluster status -b group -c yashandb -d
```

## cluster clean

本命令用于清理所有服务器上的YashanDB，也可用于执行数据库恢复前的环境清理准备。

> **Warn**：
>
> 未指定--restore时，clean操作将停止所有服务器上的数据库进程（指定--purge还会同时删除数据），并从yasom信息中删除，即yasom无法再管理该数据库集群。

| 选项            | 含义                                                                                                      |
| --------------- | --------------------------------------------------------------------------------------------------------- |
| *-c, --cluster* | YashanDB的集群名（必传参数）                                                                              |
| *-f,--force*    | 强制清理标志， 不使用此参数时提示二次确认                                                                 |
| *-r,--restore*  | 执行restore前期准备，删除各个节点/dbfiles、/local_fs目录下的全部内容，并将集群以nomount模式启动 |
| *-w, --nowait*  | 运行后不等待执行命令结果                                                                                  |
| *--purge*    | 清理集群并且删除所有节点的数据，默认为false                                                               |
| *--with-arch*   | 删除归档日志（--restore为true时生效），默认为false                                                               |
| *-d, --child*   | 展示任务以及子任务信息                                                                                    |
| *--disable*     | 屏蔽任务进度条展示                                                                                        |
| *-p, --password* | 数据库sys用户对应的密码，--restore为true时生效<br/>若已配置[操作系统认证](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证)（安装后默认已开启）则无需指定密码           |
| *--wait-timeout* | 命令执行超时时间（隐藏参数）                  |

示例

```shell
$ yasboot cluster clean -c yashandb --purge
# 全量恢复如果想要尽可能的多恢复数据，默认不要清理归档
$ yasboot cluster clean -c yashandb --restore
# 全量恢复如果只恢复到备份集的时间点，需要删除归档
$ yasboot cluster clean -c yashandb --restore --with-arch
```

## cluster stop

本命令用于停止所有服务器上的YashanDB服务。

| 选项              | 含义                                                                                  |
| ----------------- | ------------------------------------------------------------------------------------- |
| *-c, --cluster*   | YashanDB的集群名（必传参数）                                                          |
| *-f, --force*     | 强制停止的标志（kill -9），默认false                                                  |
| *-w, --nowait*    | 运行后不等待执行命令结果                                                              |
| *-d, --child*     | 展示任务以及子任务信息                                                                |
| *--disable*       | 屏蔽任务进度条展示                                                                    |
| *-s, --stop-mode* | 关库方式，可选[normal&#124;immediate&#124;abort]，默认为immediate（共享集群部署暂时无法使用） |
| *-u, --username*     | 指定数据库用户，不指定则默认使用sys用户           |
| *-p, --password*  | 数据库用户的密码<br/>若使用sys用户且已开启[操作系统认证](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证)（安装后默认开启）则无需指定密码   |
| *--wait-timeout* | 命令执行超时时间（隐藏参数）                  |

示例

```shell
$ yasboot cluster stop -c yashandb
$ yasboot cluster stop -c yashandb -f
```

## cluster start

本命令用于启动所有服务器上的YashanDB服务。

| 选项               | 含义                                                 |
| ------------------ | ---------------------------------------------------- |
| *-c, --cluster*    | YashanDB的集群名（必传参数）                         |
| *-m, --start-mode* | 启动数据库实例的模式，可选[open&#124;nomount&#124;mount]，默认open |
| *-w, --nowait*     | 运行后不等待执行命令结果                             |
| *-d, --child*      | 展示任务以及子任务信息                               |
| *--disable*        | 屏蔽任务进度条展示                                   |
| *-u, --username*     | 指定数据库用户，不指定则默认使用sys用户           |
| *-p, --password*  | 数据库用户的密码<br/>若使用sys用户且已开启[操作系统认证](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证)（安装后默认开启）则无需指定密码   |
| *--wait-timeout* | 命令执行超时时间（隐藏参数）                  |
| *--wallet-password* | 数据库钱包密码                  |

示例

```shell
$ yasboot cluster start -c yashandb
$ yasboot cluster start -c yashandb -m nomount
```

## cluster restart

本命令用于重启所有服务器上的YashanDB服务。

| 选项               | 含义                                                                                  |
| ------------------ | ------------------------------------------------------------------------------------- |
| *-c, --cluster*    | YashanDB的集群名（必传参数）                                                          |
| *-m, --start-mode* | 启动数据库实例的模式，可选[open&#124;nomount&#124;mount]，默认open                                  |
| *-w, --nowait*     | 运行后不等待执行命令结果                                                              |
| *-d, --child*      | 展示任务以及子任务信息                                                                |
| *-f, --force*      | 强制停止的标志（kill -9），默认false                                                  |
| *--disable*        | 屏蔽任务进度条展示                                                                    |
| *-s, --stop-mode*  | 关库方式，可选[normal&#124;immediate&#124;abort]，默认为immediate（共享集群部署暂时无法使用） |
| *-u, --username*     | 指定数据库用户，不指定则默认使用sys用户           |
| *-p, --password*  | 数据库用户的密码<br/>若使用sys用户且已开启[操作系统认证](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证)（安装后默认开启）则无需指定密码   |
| *--wait-timeout* | 命令执行超时时间（隐藏参数）                  |
| *--wallet-password* | 数据库钱包密码                  |

示例

```shell
$ yasboot cluster restart -c yashandb
$ yasboot cluster restart -c yashandb -m nomount -f
```

## cluster password set

本命令用于批量设置数据库集群中所有数据库的sys账号密码。

| 选项                 | 含义                                                      |
| -------------------- | --------------------------------------------------------- |
| *-c, --cluster*      | YashanDB的集群名（必传参数）                              |
| *-o, --old-password* | 旧sys密码，如果集群未在yasom中管理，则需要旧密码 |
| *-n, --new-password* | 新的sys密码（必传参数），密码需遵循[密码强度](../../../产品安全/身份标识与鉴别/密码认证/密码策略（yashan模式）.html#password_complexity)和[密码策略](../../../产品安全/身份标识与鉴别/密码认证/密码策略（yashan模式）)要求 <br/>若密码中含有OS命令相关的特殊字符（例如`@`、`/`、`.`、`!`、`$`、`'`等）需进行转义                                |
| *-w, --nowait*       | 运行后不等待执行命令结果                                  |
| *-d, --child*        | 展示任务以及子任务信息                                    |
| *--disable*          | 屏蔽任务进度条展示                                        |
| *-m,--hash-method*   | 密码的加密算法，可选[SHA256,SM3]                                        |

示例

```shell
$ yasboot cluster password set -n newpasswd -c yashandb
```

## cluster log

本命令用于收集数据库集群中所有节点上的全部日志信息和数据库配置文件。

| 选项               | 含义                                           |
| ------------------ | ---------------------------------------------- |
| *-c, --cluster*    | YashanDB的集群名（必传参数）                   |
| *-s,--start*       | 收集日志的开始时间，默认为当天零点        |
| *-e, --end*        | 收集日志的结束时间，默认为当前时间          |
| *-o, --output*     | 日志收集下载路径（--no-download为false时生效） |
| *-n,--no-download* | 收集日志但不会下载到本地，默认为false          |
| *-f, --force*      | 无需确认，默认false                            |
| *--disable*        | 屏蔽任务进度条展示                             |

示例

```shell
$ yasboot cluster log -c yashandb -n
# 指定开始时间和结束时间
$ yasboot cluster log -c yashandb -n -s '2006-01-02 15:04:05' -e '2006-01-02 15:04:05'
```

## cluster join

本命令用于根据配置文件对数据库集群进行yasom托管。

| 选项           | 含义                     |
| -------------- | ------------------------ |
| *-t, --type*   | YashanDB的集群类型       |
| *-c, --config* | 托管配置文件             |
| *-f, --force*  | 无需确认，默认false      |
| *-w, --nowait* | 运行后不等待执行命令结果 |
| *-d, --child*  | 展示任务以及子任务信息   |
| *--disable*    | 屏蔽任务进度条展示       |

示例

```shell
$ yasboot cluster join -t SE --config join_demo.toml
```


## cluster upgrade

本命令用于对数据库集群中所有节点上的数据库进行版本升级。

| 选项            | 含义                                      |
| --------------- | ----------------------------------------- |
| *-c, --cluster* | YashanDB的集群名（必传参数）              |
| *-f, --force*   | 强制升级，忽略升级前检查的错误，默认false |
| *--rolling*     | 是否滚动升级                         |
| *--keep-primary*     | 滚动升级后保留原有的主节点的身份           |
| *-w, --nowait*  | 运行后不等待执行命令结果                  |
| *-d, --child*   | 展示任务以及子任务信息                    |
| *--disable*     | 屏蔽任务进度条展示                        |
| *-u, --username*     | 指定数据库用户，必须为sys用户，不指定则默认使用sys用户           |
| *-p, --password*  |  数据库sys用户对应的密码<br/>若已开启[操作系统认证](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证)（安装后默认开启）则无需指定密码   |
| *--disk-found-path*   | 共享集群磁盘发现路径（默认为/dev/yfs）                      |
| *--system-data*       | 共享集群系统数据盘，支持输入多个，使用逗号分隔        |
| *--wait-timeout* | 命令执行超时时间（隐藏参数）                  |
| *--logic-stdby-sync-timeout* | 等待逻辑备库同步的超时时间，单位为秒（默认为600）                  |
| *--continue* | 用于不兼容版本之间滚动升级失败后，特殊情况下的继续升级                  |


示例

```shell
$ yasboot cluster upgrade --cluster yashandb
```

## cluster rollback

本命令用于对数据库集群中所有节点上的数据库进行版本回滚。

| 选项            | 含义                         |
| --------------- | ---------------------------- |
| *-c, --cluster* | YashanDB的集群名（必传参数） |
| *-w, --nowait*  | 运行后不等待执行命令结果     |
| *-d, --child*   | 展示任务以及子任务信息       |
| *--disable*     | 屏蔽任务进度条展示           |
| *-f, --force*   | 当数据库未运行时，强制回滚   |
| *--rolling*     | 是否滚动升级版本回退，不可和force一起使用               |
| *-u, --username*     | 指定数据库用户，不指定则默认使用sys用户           |
| *-p, --password*  | 数据库用户的密码<br/>若使用sys用户且已开启[操作系统认证](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证)（安装后默认开启）则无需指定密码   |
| *--wait-timeout* | 命令执行超时时间（隐藏参数）                  |

示例

```shell
$ yasboot cluster rollback -c yashandb
```

## cluster config show

本命令用于展示数据库集群的参数配置，不同于其它的config show命令，cluster config show不支持所有配置参数查询。

| 选项            | 含义                                       |
| --------------- | ------------------------------------------ |
| *-c, --cluster* | 集群名称，唯一标识（必传参数）             |
| *-q, --query*   | 参数名称，可以是模糊的参数名称（必传参数） |
| *-u, --username*     | 指定数据库用户，不指定则默认使用sys用户           |
| *-p, --password*  | 数据库用户的密码<br/>若使用sys用户且已开启[操作系统认证](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证)（安装后默认开启）则无需指定密码   |

支持模糊匹配，同时也支持精确匹配。

```shell
$ yasboot cluster config show -c yashandb -q ELECTION
+---------------------------------------------------------------------------------------+
| node   | HA_ELECTION_TIMEOUT | HA_ELECTION_ENABLED | HA_ELECTION_LEADER_LEASE_ENABLED |
+---------------------------------------------------------------------------------------+
| dn-5-1 | 9                   | FALSE               | FALSE                            |
+--------+---------------------+---------------------+----------------------------------+
| mn-1-1 | 9                   | FALSE               | FALSE                            |
+--------+---------------------+---------------------+----------------------------------+
| dn-3-1 | 9                   | FALSE               | FALSE                            |
+--------+---------------------+---------------------+----------------------------------+
| dn-4-1 | 9                   | FALSE               | FALSE                            |
+--------+---------------------+---------------------+----------------------------------+
| cn-2-1 | 9                   | FALSE               | FALSE                            |
+--------+---------------------+---------------------+----------------------------------+
```

## cluster config set

本命令用于对集群粒度的配置参数进行修改。

| 选项             | 含义                                 |
| ---------------- | ------------------------------------ |
| *-c, --cluster*  | 集群名称（必传参数）                 |
| *-k, --key*      | 修改的配置参数的名称（必传参数），单机支持修改PROTECTION_MODE     |
| *-v, --value*    | 修改参数的目标值（必传参数）         |
| *-r, --rollback* | 支持回滚集群参数（与force参数互斥）  |
| *-s, --scope*    | 支持设置参数的范围                   |
| *-f, --force*    | 支持强制修改参数（即使节点状态异常） |
| *-w, --nowait*   | 运行后不等待执行命令结果             |
| *-d, --child*    | 展示任务以及子任务信息               |
| *--disable*      | 屏蔽任务进度条展示                   |
| *-u, --username*     | 指定数据库用户，不指定则默认使用sys用户           |
| *-p, --password*  | 数据库用户的密码<br/>若使用sys用户且已开启[操作系统认证](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证)（安装后默认开启）则无需指定密码   |

修改集群参数。

```shell
$ yasboot cluster config set -c yashandb -k REDO_BUFFER_SIZE -v 20M
```

强制修改集群参数。

```shell
$ yasboot cluster config set -c yashandb -k REDO_BUFFER_SIZE -v 20M -f
```

修改失败后回滚参数。

```shell
$ yasboot cluster config set -c yashandb -k HA_HEARTBEAT_INTERVAL -v 500 -r
```

## cluster config unset

本命令用于消除已设置的配置参数。

| 选项            | 含义                             |
| --------------- | -------------------------------- |
| *-c, --cluster* | 集群名称（必传参数）             |
| *-k, --key*     | 修改的配置参数的名称（必传参数） |
| *-w, --nowait*  | 运行后不等待执行命令结果         |
| *-d, --child*   | 展示任务以及子任务信息           |
| *--disable*     | 屏蔽任务进度条展示               |
| *-u, --username*     | 指定数据库用户，不指定则默认使用sys用户           |
| *-p, --password*  | 数据库用户的密码<br/>若使用sys用户且已开启[操作系统认证](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证)（安装后默认开启）则无需指定密码   |

示例

```shell
$ yasboot cluster config unset -c yashandb -k HA_ELECTION_ENABLED
```

## cluster esn gen

本命令用于根据使用的服务器生成esn信息。

| 选项            | 含义                                    |
| --------------- | ---------------------------------------|
| *-c, --cluster* | 集群名称，唯一标识（必传参数）            |
| *-i, --info*    | 服务器信息，通常无需填写，工具可自动获取   |
| *-v, --version* | esn版本信息，默认v1                      |

```shell
$ yasboot cluster esn gen -c yashandb
```

## cluster license update

本命令用于更新数据库license。

| 选项               | 含义                                                 |
| ------------------ | ---------------------------------------------------- |
| *-c, --cluster*    | YashanDB的集群名（必传参数）                         |
| *-w, --nowait*     | 运行后不等待执行命令结果                             |
| *-d, --child*      | 展示任务以及子任务信息                               |
| *--disable*        | 屏蔽任务进度条展示                                   |
| *-u, --username*     | 指定数据库用户，不指定则默认使用sys用户           |
| *-p, --password*  | 数据库用户的密码<br/>若使用sys用户且已开启[操作系统认证](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证)（安装后默认开启）则无需指定密码   |
| *--wait-timeout* | 命令执行超时时间（隐藏参数）                  |
| *-f, --file*     | license文件路径（建议使用绝对路径）                  |

```shell
$ yasboot cluster license update -c yashandb -f /var/database/license.lic
```

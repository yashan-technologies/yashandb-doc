## group config show

本命令用于展示节点组的配置信息。

|  选项| 含义|
| ---------------- | --------------------------------- |
| *-c, --cluster*  | YashanDB的集群名（必传参数）                  |
| *-g, --group-id* | 组ID（可以通过yasboot cluster status命令查看，nodeid中短横线前的数值为group-id，例如`1-1:1`的组ID为1）（必传参数） |
| *-q, --query*    | 查询组配置中某一个参数            |
| *-a, --all*      | 展示该组所有的参数                |
| *--disable*     | 屏蔽运行的进度信息                              |
| *-u, --username*     | 指定数据库用户，不指定则默认使用sys用户           |
| *-p, --password*  | 数据库用户的密码<br/>若使用sys用户且已开启[操作系统认证](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证.md)（安装后默认开启）则无需指定密码   |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$ yasboot group config show -c yashandb -g 1
 mn-1-1                                   
------------------------------------------
 CONTROL_FILES=('?/dbfiles/ctrl1', '?/dbf 
 iles/ctrl2', '?/dbfiles/ctrl3')          
------------------------------------------
 DDL_LOCK_TIMEOUT=60                      
------------------------------------------
 DEFAULT_TABLE_TYPE=LSC                   
------------------------------------------
 DIN_ADDR=192.168.1.2:1679              
------------------------------------------
 LISTEN_ADDR=192.168.1.2:1678           
------------------------------------------
 NODE_ID=1-1:1                            
------------------------------------------
 RUN_LOG_LEVEL=DEBUG                      
------------------------------------------
```

## group config set

本命令用于设置节点组的配置。

|  选项| 含义|
| ------------------ | ----------------------------------- |
| *-c, --cluster*  | YashanDB的集群名（必传参数）                    |
| *-g, --group-id* | 组ID（可以通过yasboot cluster status命令查看，nodeid中短横线前的数值为group-id，例如`1-1:1`的组ID为1） |
| *-gt, --group-type*  | 组类型名称，db：单机部署、ce：共享集群/分布式集群、mn：分布式MN组、cn：分布式CN节点、dn：分布式DN组）            |
| *-k, --key*      | 设置参数的名称（必传参数），单机支持修改PROTECTION_MODE                      |
| *-v, --value*    | 设置参数key对应的值（必传参数）                |
| *-f, --force*    | 强制设置配置参数                    |
| *-r, --rollback* | 设置回滚参数（与force参数互斥）     |
| *-s, --scope*    | 支持设置参数的范围                   |
| *-w, --nowait*   | 运行后不等待执行命令结果            |
| *-d, --child*    | 展示任务以及子任务信息              |
| *--disable*      | 屏蔽任务进度条展示                  |
| *-u, --username*     | 指定数据库用户，不指定则默认使用sys用户           |
| *-p, --password*  | 数据库用户的密码<br/>若使用sys用户且已开启[操作系统认证](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证.md)（安装后默认开启）则无需指定密码   |
| *-h,--help*        | 查看当前命令的帮助信息 |

`-g`和`-gt`参数二者必选其一。

示例

```shell
$ yasboot group config set -k MAX_WORKERS -v 10 -c yashandb -g 1
+--------------------------------------------------------------------------------------------------------------+
| type | uuid             | name                 | hostid | index    | status  | return_code | progress | cost |
+--------------------------------------------------------------------------------------------------------------+
| task | b61ac050e4bdd753 | GroupParentConfigSet | -      | yashandb | SUCCESS | 0           | 100      | 1    |
+------+------------------+----------------------+--------+----------+---------+-------------+----------+------+
task completed, status: SUCCESS
```

修改组类型为dn的节点。

```shell
$ yasboot group config set -k MAX_WORKERS -v 1024 -c yashandb -gt dn
```

强制修改组节点的配置参数。

```shell
$ yasboot group config set -k MAX_WORKERS -v 1024 -c yashandb -g 3 --force
```

设置修改组节点参数失败后回滚。

```shell
$ yasboot group config set -k MAX_WORKERS -v 1024 -c yashandb -gt dn -r
```

## group config unset

本命令用于将节点组配置恢复为数据库的默认值，其中[建库参数](../建库参数.md)可能与部署时分配的默认值不一致。

|  选项| 含义|
| ---------------- | --------------------------------- |
| *-c, --cluster*  | YashanDB的集群名（必传参数）                  |
| *-g, --group-id* | 组ID（可以通过yasboot cluster status命令查看，nodeid中短横线前的数值为group-id，例如`1-1:1`的组ID为1）（必传参数） |
| *-k, --key*      | 消除参数的名称（必传参数）                    |
| *-w, --nowait*   | 运行后不等待执行命令结果          |
| *-d, --child*    | 展示任务以及子任务信息            |
| *--disable*      | 屏蔽任务进度条展示                |
| *-u, --username*     | 指定数据库用户，不指定则默认使用sys用户           |
| *-p, --password*  | 数据库用户的密码<br/>若使用sys用户且已开启[操作系统认证](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证.md)（安装后默认开启）则无需指定密码   |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$ yasboot group config unset -c yashandb -g 1 -k SLOW_LOG_SQL_MAX_LEN 
```

## group status

本命令用于查看组内节点运行的状态。

|  选项| 含义|
| ---------------- | ----------------------------------- |
| *-c, --cluster*  | YashanDB的集群名（必传参数）                    |
| *-g, --group-id* | 组ID（可以通过yasboot cluster status命令查看，nodeid中短横线前的数值为group-id，例如`1-1:1`的组ID为1）   |
| *-d, --detail*   | 是否展示运行状态的细节，默认为false |
| *-u, --username*     | 指定数据库用户，不指定则默认使用sys用户           |
| *-p, --password*  | 数据库用户的密码<br/>若使用sys用户且已开启[操作系统认证](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证.md)（安装后默认开启）则无需指定密码   |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$ yasboot group status -c yashandb -g 1 -d
```

## group start

本命令用于启动组内的所有节点。

|  选项| 含义|
| ------------------ | ------------------------------------------------ |
| *-c, --cluster*    | YashanDB的集群名（必传参数）                                 |
| *-g, --group-id*   | 组ID（可以通过yasboot cluster status命令查看，nodeid中短横线前的数值为group-id，例如`1-1:1`的组ID为1）（必传参数）                |
| *-m, --start-mode* | 启动到哪个阶段，可选[mount&#124;nomount&#124;open]，默认为open<br/>共享集群/分布式集群部署暂无法启动到mount阶段 |
| *-w, --nowait*     | 运行后不等待执行命令结果                         |
| *-d, --child*      | 展示任务以及子任务信息                           |
| *--disable*        | 屏蔽运行的进度信息                               |
| *-u, --username*     | 指定数据库用户，不指定则默认使用sys用户           |
| *-p, --password*  | 数据库用户的密码<br/>若使用sys用户且已开启[操作系统认证](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证.md)（安装后默认开启）则无需指定密码   |
| *--wait-timeout* | 命令执行超时时间（隐藏参数）                  |
| *--wallet-password* | 数据库钱包密码                  |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$ yasboot group start -c yashandb -g 1 -m nomount
```

## group stop

本命令用于停止组内的所有节点。

|  选项| 含义|
| ---------------- | --------------------------------- |
| *-c, --cluster*  | YashanDB的集群名（必传参数）                  |
| *-g, --group-id* | 组ID（可以通过yasboot cluster status命令查看，nodeid中短横线前的数值为group-id，例如`1-1:1`的组ID为1）（必传参数） |
| *-f, --force*    | 是否强制停止节点，默认不强制      |
| *-w, --nowait*   | 运行后不等待执行命令结果          |
| *-d, --child*    | 展示任务以及子任务信息            |
| *--disable*      | 屏蔽运行的进度信息                |
| *-s, --stop-mode*     | 关库方式，可选[normal&#124;immediate&#124;abort]，默认为immediate（共享集群/分布式集群部署暂时无法使用）   |
| *-u, --username*     | 指定数据库用户，不指定则默认使用sys用户           |
| *-p, --password*  | 数据库用户的密码<br/>若使用sys用户且已开启[操作系统认证](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证.md)（安装后默认开启）则无需指定密码   |
| *--wait-timeout* | 命令执行超时时间（隐藏参数）                  |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$ yasboot group stop -c yashandb -g 1
```

## group restart

本命令用于重启组内的所有节点。

|  选项| 含义|
| ------------------ | ------------------------------------------------ |
| *-c, --cluster*    | YashanDB的集群名（必传参数）                                 |
| *-g, --group-id*   | 组ID（可以通过yasboot cluster status命令查看，nodeid中短横线前的数值为group-id，例如`1-1:1`的组ID为1）（必传参数）                |
| *-m, --start-mode* | 启动到哪个阶段，可选[mount&#124;nomount&#124;open]，默认为open<br/>共享集群/分布式集群部署暂无法启动到mount阶段 |
| *-f, --force*      | 是否强制停止节点，默认不强制                     |
| *-w, --nowait*     | 运行后不等待执行命令结果                         |
| *-d, --child*      | 展示任务以及子任务信息                           |
| *--disable*        | 屏蔽运行的进度信息                               |
| *-s, --stop-mode*     | 关库方式，可选[normal&#124;immediate&#124;abort]，默认为immediate（共享集群/分布式集群部署暂时无法使用）   |
| *-u, --username*     | 指定数据库用户，不指定则默认使用sys用户           |
| *-p, --password*  | 数据库用户的密码<br/>若使用sys用户且已开启[操作系统认证](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证.md)（安装后默认开启）则无需指定密码   |
| *--wait-timeout* | 命令执行超时时间（隐藏参数）                  |
| *--wallet-password* | 数据库钱包密码                  |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$ yasboot group restart -c yashandb -g 1 -f -m mount
```

## group add

本命令用于对存算一体分布式集群部署中的DN节点组进行扩容。

|  选项| 含义|
| --------------- |-------------------------|
| *-c, --cluster* | 集群名称（必传参数）               |
| *-t, --toml*    | yasboot config group gen命令生成的集群扩容配置文件`{集群名称}_add.toml`的路径（必传参数）        |
| *-d, --child*   | 展示任务以及子任务信息             |
| *--disable*     | 屏蔽运行的进度信息               |
| *--auto-redistribute*  | 是否自动重分布，默认为true。若指定为false，扩容DN组后还需手动执行yasboot dataspace redistribute进行重分布，命令详情请查阅[yasboot dataspace](yasboot dataspace)         |
| *--clean-residual-immediately*  | 是否自动清理chunk表空间 |
| *--dataspace-id*    | dataspace id（route$中的DS_ID），默认为0  |
| *-p, --password* | 数据库sys用户对应的密码<br/>若已开启[操作系统认证](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证.md)（安装后默认开启）则无需指定密码                          |
| *--wait-timeout* | 命令执行超时时间（隐藏参数）                  |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$ yasboot group add -t yashandb_add.toml -c yashandb
```

## group remove

本命令用于对存算一体分布式集群部署中的DN节点组进行缩容。

|  选项| 含义|
|-----------------|-------------------------------------------|
| *-c, --cluster* | YashanDB的集群名                              |
| *--group-ids*   | 组ID（可以通过yasboot cluster status命令查看，nodeid中短横线前的数值为group-id，例如`1-1:1`的组ID为1），支持多个，使用逗号分隔 |
| *-f, --force*   | 跳过确认直接执行命令                                |
| *--purge*   | 删除缩容节点的data数据                             |
| *-w, --nowait*  | 运行后不等待执行命令结果                              |
| *-d, --child*   | 展示任务以及子任务信息                               |
| *--disable*     | 屏蔽运行的进度信息                                 |
| *--clean*       | 清理所有扩容失败的节点组，默认为false            |
| *--dataspace-id*    | dataspace id（route$中的DS_ID），默认为0  |
| *-u, --username*     | 指定数据库用户，不指定则默认使用sys用户           |
| *-p, --password*  | 数据库用户的密码<br/>若使用sys用户且已开启[操作系统认证](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证.md)（安装后默认开启）则无需指定密码   |
| *--with-host*  | 成功删除节点组后，继续删除当前集群中无数据库节点、无yasom进程的空服务器的信息（如果使用了--nowait，则该参数失效）|
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$ yasboot group remove -c yashandb --group-ids 3 --purge -f
```

## group auto-election on

本命令用于开启节点组自动选主，仅适用于单机部署和存算一体分布式集群部署。

|  选项| 含义|
|-----------------|-------------------------------------------|
| *-c, --cluster* | YashanDB的集群名                              |
| *-g, --group-id*   | 组ID（可以通过yasboot cluster status命令查看，nodeid中短横线前的数值为group-id，例如`1-1:1`的组ID为1）（必传参数）                |
| *-w, --nowait*  | 运行后不等待执行命令结果                              |
| *-d, --child*   | 展示任务以及子任务信息                               |
| *--disable*     | 屏蔽运行的进度信息                                 |
| *-u, --username*     | 指定数据库用户，不指定则默认使用sys用户           |
| *-p, --password*  | 数据库用户的密码<br/>若使用sys用户且已开启[操作系统认证](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证.md)（安装后默认开启）则无需指定密码   |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$ ./bin/yasboot group auto-election on -c yashandb --group-id 2
```

## group auto-election off

本命令用于关闭节点组自动选主，仅适用于单机部署和存算一体分布式集群部署。

|  选项| 含义|
|-----------------|-------------------------------------------|
| *-c, --cluster* | YashanDB的集群名                              |
| *-g, --group-id*   | 组ID（可以通过yasboot cluster status命令查看，nodeid中短横线前的数值为group-id，例如`1-1:1`的组ID为1）（必传参数）                |
| *-w, --nowait*  | 运行后不等待执行命令结果                              |
| *-d, --child*   | 展示任务以及子任务信息                               |
| *--disable*     | 屏蔽运行的进度信息                                 |
| *-u, --username*     | 指定数据库用户，不指定则默认使用sys用户           |
| *-p, --password*  | 数据库用户的密码<br/>若使用sys用户且已开启[操作系统认证](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证.md)（安装后默认开启）则无需指定密码   |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$ ./bin/yasboot group auto-election off -c yashandb --group-id 2
```

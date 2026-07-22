## config node gen

本命令用于生成扩容相关的配置文件hosts_add.toml和${cluster}_add.toml。

|  选项| 含义|
| -------------------- | --------------------- |
| *-c, --cluster*      | 生成的集群名称（必传参数）                                   |
| *-u, --username*     | 服务器SSH用户名      |
| *--groupname*         | 用户组名称，默认和`username`相同                             |
| *-p, --password*     | SSH登录密码        |
| *-N, --no-password*  | SSH免密登录        |
| *--ip*               | 部署的IP地址，多个IP间用逗号`,`隔开                                 |
| *--cn-ip*                | 分布式集群部署的计算节点IP地址，多个IP间用逗号`,`隔开            |
| *--dn-ip*                | 分布式集群部署的数据节点IP地址，多个IP间用逗号`,`隔开            |
| *--port*             | 服务器SSH连接端口    |
| *-i, --install-path* | 数据库安装路径（HOME目录）                                   |
| *--data-path*        | 数据库实例的DATA目录                                         |
| *-f, --force*          | 是否强制部署数据库，强制表示不会检查当前服务器运行状态是否能够部署 |
| *-g, --group-id*       | 组ID（可以通过yasboot cluster status命令查看，nodeid中短横线前的数值为group-id，例如`1-1:1`的组ID为1），默认为1      |
| *--node*               | 新增的总节点数（分布式集群中表示CN数量）。默认为1（分布式集群中默认在一个cn-ip上创建一个CN） |
| *--host-id*           | 已部署服务器的ID，多台服务器使用逗号分隔（废弃参数）                 |
| *--host-ids*           | 已部署服务器的ID，多台服务器使用逗号分隔                 |
| *--begin-port*        | 起始端口1688       |
| *--sys-password* | 数据库sys用户对应的密码                         |
| *-su,--sudo-username* | 具有sudo权限的SSH用户，缺省使用同--username相同的值（用于执行需要sudo权限的命令，例如创建cgroup目录）   |
| *-sp,--sudo-password* | 具有sudo权限的SSH用户密码，缺省使用同--password相同的值        |
| *--no-add-yasdba*       | 不添加安装用户到YASDBA用户组<br>**不推荐使用**，指定后安装用户将无法通过[操作系统认证](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证.md)免密登录数据库          |
| *--ssl-protocol*      | 指定配置数据库[客户端<->服务器可信信道](../../../产品安全/加密支持/可信信道/00可信信道.md)的协议，目前仅支持SSL <br/> 须配合--ssl-path使用   |
| *--ssl-path*          | 指定SSL相关证书的存放路径，须配合--ssl-protocol使用 <br/> 执行数据库安装时，会将该路径下的所有文件全部拷贝至每台服务器的$YASDB_HOME/ssl路径下                    |
| *--log-path*          | yasagent、yasom、数据库run.log、slow.log的路径                   |
| *-m, --mode*          | 语法模式，可选项为[yashan,mysql]，默认为yashan，仅适用于单机部署                   |
| *--vips*              | 共享集群扩容时，若原集群已配置开启VIP，需要通过此参数指定扩容实例的VIP配置信息列表（格式为IP地址/子网掩码/网卡名，例如：192.168.60.4/255.255.255.0/ens192），VIP个数与扩容节点个数一致，多个VIP配置信息之间用逗号`,`隔开 |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例1（在新增服务器上扩容节点，生成hosts_add.toml和${cluster}_add.toml文件）

```shell
$ yasboot config node gen -c yashandb -u yashan -p password --ip ip1,ip2...ipn --port 22  --install-path /var/database/yashan -g 1 --node 2 --vips 192.168.60.4/24/ens192,192.168.60.5/24/ens192
```

> **Note**:
>
> 若部署时使用了--ssl-path和--ssl-protocol参数，扩容时生成配置信息也需使用这2个参数，否则会导致扩容失败。

示例2（在原有服务器上扩容节点，生成${cluster}_add.toml）

```shell
$ yasboot config node gen -c yashandb --host-ids host0001,host0002 -g 1 --node 2
```

示例3（在分布式集群部署下，新增服务器上扩容CN/DN节点，生成hosts_add.toml和${cluster}_add.toml文件）

```shell
$ yasboot config node gen -c yashandb -u yashan -p password --dn-ip ip1 --cn-ip ip2 --data /dev/nvme0n1 --install-path /var/database/yashan
```

## config host gen

本命令用于生成服务器检查的配置文件yascheck.toml。

|  选项| 含义|
| -------------- | ---------------------------------- |
| *\-o,--output* | 配置文件生成的路径（默认当前目录） |
| *--ip*         | 此IP将作为网络测试的IP地址         |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```bash
$ yasboot config host gen -o ./ --ip 192.168.x.x
```

## config sql gen

本命令用于SQL信息收集的配置文件sqlcollect.toml。

|  选项| 含义|
| -------------- | ---------------------------------- |
| *\-o,--output* | 配置文件生成的路径（默认当前目录） |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```bash
$ yasboot config sql gen -o ./
```

## config patrol gen

本命令用于根据输入的各选项生成巡检策略配置文件。

|  选项| 含义|
| ----------------------- | --------------------- |
| *-c, --cluster*         | YashanDB的集群名（必传参数）                                 |
| *-s, --strategy-name*   | 巡检策略名称 （必传参数）                                    |
| *-t, --strategy-type*   | 巡检策略类型：<br/>\* PERIOD：周期性策略，应用后可以周期性执行多次，默认值<br/>\* TIMING：定时巡检策略，应用后定时执行一次 |
| *-ce,--cron-expression* | cron表达式，用于设置巡检时间，由五个字段组成，分别表示分、小时、日期、月份、星期 |
| *-f,--frequency*        | 巡检频率：<br/>\* monthly：每月<br/>\* weekly：每周，默认值<br/>\* daily：每天<br/>\* hourly：每小时 |
| *-d,--days*             | 巡检具体的执行天数 |
| *-st, --start-time*     | 巡检的具体开始时间 |
| *-sp,--store-path*      | 巡检文件存放路径   |
| *-sd,--store-days*       | 巡检文件的最大保存天数，默认全部保存                         |
| *-sn,--store-num*       | 巡检文件的最大保存数量，默认全部保存                         |
| *--yascheck-toml*      | yascheck.toml配置文件                                        |
| *--sql-toml*          | sqlcollect.toml配置文件                                      |
| *--patrol-module*      | 巡检模块，可选`host`、`sql`和`gstack`，默认收集所有模块，填写多个使用逗号`,`隔开 |
| *-F,--format*          | 检查结果格式，可选json和html                                 |
| *--node-ids*           | SQL收集的节点ID，多个ID间用逗号`,`隔开，默认为`1-1`（可以使用`all`表示收集所有节点） |
| *-o,--output*           | 生成的配置文件保存路径，默认是当前路径 |
| *-h,--help*        | 查看当前命令的帮助信息 |

```shell
# 通过参数`--cron-expression`设置巡检时间并生成配置文件
$ yasboot config patrol gen -c yashandb -s ps01  --cron-expression "10 12 ? 1,5,6,0 *" 

# 通过参数`--frquency、--days、--start-time`设置巡检时间并生成配置文件
$ yasboot config patrol gen -c yashandb -s ps01  -f weekly -d 1,5-7  -st 12:10

# 生成巡检模块为host和gstack的配置文件
$ yasboot config patrol gen -c yashandb -s ps01  --cron-expression "10 12 ? 1,5,6,0 *" --patrol-module host,gstack
```

## config group gen

本命令用于生成扩容相关的配置文件hosts_add.toml和${cluster}_add.toml。

|  选项| 含义|
| -------------------- |------|
| *-c, --cluster*      | 生成的集群名称（必传参数）                               |
| *-u, --username*     | 服务器SSH用户名                                    |
| *--groupname*     | 服务器SSH用户组名                                   |
| *-p, --password*     | SSH登录密码                                     |
| *-N, --no-password*  | SSH免密登录                                     |
| *--ip*               | 部署的IP地址，多个IP间用逗号`,`隔开                      |
| *--port*             | 服务器SSH连接端口                                   |
| *-i, --install-path* | 数据库安装路径（HOME目录）                             |
| *--host-id*            | 已部署服务器的ID，多个ID间用逗号`,`隔开（废弃参数）                     |
| *--host-ids*            | 已部署服务器的ID，多个ID间用逗号`,`隔开                     |
| *--data-path*        | 数据库实例的DATA目录                                |
| *-f, --force*          | 是否强制部署数据库，强制表示不会检查当前服务器运行状态是否能够部署            |
| *--node*               | 新增节点组中的节点数，默认为1                             |
| *--group*              | 新增的节点组数量，默认为1                               |
| *--begin-port*        | 起始端口1688       |
| *--sys-password* | 数据库sys用户对应的密码                         |
| *-su,--sudo-username* | 具有sudo权限的SSH用户，缺省使用同--username相同的值（用于执行需要sudo权限的命令，例如创建cgroup目录）   |
| *-sp,--sudo-password* | 具有sudo权限的SSH用户密码，缺省使用同--password相同的值        |
| *--no-add-yasdba*       | 不添加安装用户到YASDBA用户组<br>**不推荐使用**，指定后安装用户将无法通过[操作系统认证](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证.md)免密登录数据库          |
| *--ssl-protocol*      | 指定配置数据库[客户端<->服务器可信信道](../../../产品安全/加密支持/可信信道/00可信信道.md)的协议，目前仅支持SSL <br/> 须配合--ssl-path使用   |
| *--ssl-path*          | 指定SSL相关证书的存放路径，须配合--ssl-protocol使用 <br/> 执行数据库安装时，会将该路径下的所有文件全部拷贝至每台服务器的$YASDB_HOME/ssl路径下                    |
| *--log-path*          | yasagent、yasom、数据库run.log、slow.log的路径                   |
| *-t, --type*          | 新增的节点组类型。默认为DN（隐藏参数）                   |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例1（在新增服务器上扩容节点，生成hosts_add.toml和${cluster}_add.toml文件）

```shell
$ yasboot config group gen -c yashandb -u yashan -p password --ip ip1,ip2...ipn --port 22  --install-path /var/database/yashan --group 2 --node 3
```

> **Note**:
>
> 若部署时使用了--ssl-path和--ssl-protocol参数，扩容时生成配置信息也需使用这2个参数，否则会导致扩容失败。

示例2（在原有服务器上扩容节点，生成${cluster}_add.toml）

```shell
$ yasboot config group gen -c yashandb --host-ids host0001,host0002 --group 2 --node 3
```

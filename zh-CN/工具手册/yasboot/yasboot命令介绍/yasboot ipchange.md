由yasboot所管理的数据库集群中的服务器IP地址发生变更时，用户可以调用`ipchange yasom`和`ipchange yasagent`命令更新yasom侧IP的配置，确保该服务器仍可被yasboot管理。并使用`ipchange host`更新YashanDB侧IP的配置，使集群在新IP下正常工作。

IP更换执行的顺序为yasom > yasagent > yasdb，若无需更新某个工具/服务器的IP可跳过，继续更新下一个工具/服务器的IP，但不能乱序操作，具体操作请查阅[更换服务器IP](../../../安装和升级/重部署/更换服务器IP.md)。

## ipchange yasom

本命令用于更换主yasom的IP并重启yasom，yasom会在重启后监听新的IP地址和原有的端口。

|  选项| 含义|
| ----------------   | -----------------------------  |
| *-t, --toml*        | 服务器配置文件`hosts.toml`的路径（必传参数）     |
| *-n, --new-ip*      | 主yasom所在服务器的新IP（必传参数）               |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例：

```shell
$ yasboot ipchange yasom -t hosts.toml -n 192.168.1.2

stop yasom successfully
start yasom successfully
restart yasom successfully
yasom change ip success
```

## ipchange yasagent

本命令用于更换yasagent的IP并重启yasagent，yasagent会在重启后监听新的IP地址和原有的端口。

本命令一次只能更换一个服务器上的yasagent的IP。如需更新多个服务器上yasagent的IP，需对每个需更换IP的yasagent所在服务器使用此命令。

|  选项| 含义|
| ----------------   | -----------------------------  |
| *-t, --toml*        | 服务器配置文件`hosts.toml`的路径（必传参数）  |
| *-n, --new-ip*        | yasagent上服务器的新IP（必传参数）               |
| *--host-id*           | 要更换yasagent所在服务器的ID，例如：host0001，可通过hosts.toml文件获取（必传参数） |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例：

```shell
$ yasboot ipchange yasagent -t hosts.toml -n 192.168.1.2 --host-id host0001

stop yasagent successfully
start yasagent successfully
restart yasagent successfully
yasagent change ip success
```

## ipchange host

使用本命令更换服务器IP的前提条件：

- 如需更换yasom的IP，已完成相应操作。
- 如需更换yasagent的IP，已完成更换每个待更换IP的yasagent的IP。

本命令用于更换数据库实例的IP，使用本命令时，会自动关闭数据库实例。

本命令仅适用于单机部署。

|  选项| 含义|
| ----------------   | -----------------------------  |
| *-t, --toml*        | 服务器配置文件`hosts.toml`的路径（必传参数）  |
| *-l, --listen-ip*        | 数据库的监听地址的新IP，和--listen-cidr二选一指定               |
| *-r, --replica_ip*        | 主备复制链路地址的新IP，和--replica-cidr二选一指定               |
| *-lc, --listen-cidr*     | 数据库的监听地址的新网段，可以填写多个，例如: 192.168.1.2/24,0.0.0.0/0，和--listen-ip二选一指定 |
| *-rc, --replica-cidr*     | 主备复制链路地址的新网段，可以填写多个，例如: 192.168.1.2/24,0.0.0.0/0，和--replica-ip二选一指定 |
| *--host-id*           | 服务器ID，可通过hosts.toml文件获取，例如：host0001，可参见hosts.toml |
| *-s, --start*           | 完成本次IP更换后，自动重启数据库 |
| *-u, --username*     | 指定数据库用户，不指定则默认使用sys用户           |
| *-p, --password*  | 数据库用户的密码<br/>若使用sys用户且已开启[操作系统认证](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证.md)（安装后默认开启）则无需指定密码   |
| *-h,--help*        | 查看当前命令的帮助信息 |

当`--listen-ip`和`--listen-cidr`均不指定时，yasom会判断当前的数据库监听地址IP是否可用。若可用，则不修改。若不可用，则改为yasagent的IP。`--replica-ip`和`--replica-cidr`同理。

示例：

1. 更换IP。

    场景1：使用新IP，更换某个服务器（例如host0001）上数据库实例的IP。
    ```shell
    $ yasboot ipchange host -t hosts.toml -l 192.168.1.2 -r 192.168.1.2 --host-id host0001

    host host0001 change ip success
    ```

    场景2：使用新网段，更换某个服务器（例如host0001）上数据库实例的IP。
    ```shell
    $ yasboot ipchange host -t hosts.toml -lc 192.168.1.1/24 -rc 192.168.1.1/24 --host host0001

    new replication ip is 192.168.1.2
    new listen ip is 192.168.1.2
    host0001 change ip success
    ```

    场景3：使用yasagent的IP，一次更换全部数据库实例的IP。
    ```shell
    $ yasboot ipchange host -t hosts.toml

    host0001 change ip success
    ```

    场景4：使用新网段，一次更换全部数据库实例的IP。
    ```shell
    $ yasboot ipchange host -t hosts.toml -lc 192.168.1.1/24,192.168.2.1/24 -lr 192.168.1.1/24,192.168.2.1/24

    host0001 change ip success
    ```

2. 更换完IP后，重启数据库实例。

    - 方式一：更换最后一个服务器IP时，通过`-s`参数实现更换完成后启动数据库。
    ```shell
    $ yasboot ipchange host -t hosts.toml -l 192.168.1.2 -r 192.168.1.2 --host-id host0001 -s

    host host0001 change ip success
    start cluster now
    +-----------------------------------------------------------------------------------------------------------+
    | type | uuid             | name              | hostid | index    | status  | return_code | progress | cost |
    +-----------------------------------------------------------------------------------------------------------+
    | task | 01dd119a4034dd02 | StartYasdbCluster | -      | yashandb | SUCCESS | 0           | 100      | 1    |
    +------+------------------+-------------------+--------+----------+---------+-------------+----------+------+
    task completed, status: SUCCESS
    ```

    - 方式二：全部更换完成后，使用[cluster start](yasboot cluster)命令手动启动数据库实例。
    ```shell
    $ yasboot cluster start -c yashandb

    +-----------------------------------------------------------------------------------------------------------+
    | type | uuid             | name              | hostid | index    | status  | return_code | progress | cost |
    +-----------------------------------------------------------------------------------------------------------+
    | task | 01dd119a4034ru01 | StartYasdbCluster | -      | yashandb | SUCCESS | 0           | 100      | 1    |
    +------+------------------+-------------------+--------+----------+---------+-------------+----------+------+
    task completed, status: SUCCESS
    ```

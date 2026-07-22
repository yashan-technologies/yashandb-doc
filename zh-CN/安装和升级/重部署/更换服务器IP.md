在线重部署是一个可计划的事项，应与其所依赖的前置操作（例如服务器IP变更）一起规划和执行。

本文主要介绍当单机部署的数据库集群中一台或多台服务器的IP发生变更时，管理员如何通过[yasboot ipchange命令](../../工具手册/yasboot/yasboot命令介绍/yasboot ipchange)进行数据库层面的IP更换操作，最终使得数据库恢复可用。本文以一主两备环境中的两台服务器IP发生变更为例进行描述：

|  服务器名称| 旧IP| 角色| 新IP|
| -------- | --------------- | ---- | --------------- |
| host0001    | **192.168.1.2** | 主   | **192.168.1.5** |
| host0002    | **192.168.1.3** | 备   | **192.168.1.6** |
| host0003    | 192.168.1.4     | 备   | 192.168.1.4     |

IP更换执行的顺序为yasom > yasagent > yasdb，若无需更新某个工具/服务器的IP可跳过，继续更新下一个工具/服务器的IP，但不能乱序操作。

## 前提条件

执行下述操作前，请确保新IP的环境仍满足YashanDB的各项部署要求，具体请查阅[安装前准备](../安装部署/安装前准备/00安装前准备)。

## 步骤1：更换yasom IP

1. 以安装用户登录数据库安装服务器（即安装部署时生成hosts.toml文件的服务器）。

2. 进入YashanDB安装目录，查看主yasom进程所在服务器信息。

    ```shell
    $ cd /home/yashan/install
    $ cat hosts.toml
    ```

    查看hosts.toml文件中的如下内容可以获取主yasom进程所在服务器的相关信息：

    ```toml
    [om]
      hostid = "host0001"
      [om.config]
        LISTEN_ADDR = "192.168.1.2:1675"
    ```

    根据上述信息，yasom部署于hostid为host0001的服务器上，该服务器的IP为192.168.1.2，需要更换。

3. 更换主yasom进程的IP。

    ```shell
    $ cd /home/yashan/install
    $ yasboot ipchange yasom -t hosts.toml -n 192.168.1.5
    ```

## 步骤2：更换yasagent IP

1. 查看yasagent服务所在服务器信息。

    查看hosts.toml文件中的如下内容可以获取yasagent服务所在服务器的相关信息：

    ```toml
    [[host]]
      hostid = "host0001"
    [host.yasagent]
        [host.yasagent.config]
          LISTEN_ADDR = "192.168.1.2:1676"

    [[host]]
      hostid = "host0002"
    [host.yasagent]
        [host.yasagent.config]
          LISTEN_ADDR = "192.168.1.3:1676"

    [[host]]
      hostid = "host0003"
    [host.yasagent]
        [host.yasagent.config]
          LISTEN_ADDR = "192.168.1.4:1676"

    ```

    根据上述信息，hostid为host0001和host0002的服务器上的yasagent使用了旧IP（192.168.1.2和192.168.1.3），需要更换。hostid为host0003服务器上的yasagent无需更换IP。

2. 执行如下命令，依次更换host0001和host0002服务器上的yasagent的IP。

    ```shell
    # 更换host0001服务器上的yasagent IP
    $ yasboot ipchange yasagent -t hosts.toml -n 192.168.1.5 --host-id host0001

    # 更换host0002服务器上的yasagent IP
    $ yasboot ipchange yasagent -t hosts.toml -n 192.168.1.6 --host-id host0002
    ```

## 步骤3：启动yasom和yasagent进程

执行如下命令启动/重启所有yasom和yasagent进程：

```shell
$ yasboot process yasom restart -c yashandb -t hosts.toml
$ yasboot process yasagent restart -c yashandb -t hosts.toml
```

## 步骤4：更换yasdb IP

更换yasdb IP包括对yasdb进程的数据库监听地址和主备复制链路地址的更换，yasdb进程所在的服务器信息与上述yasagent服务所在服务器信息一致。

> **Note**:
>
> 更换yasdb IP时，当前数据库环境中的所有yasdb进程将被停止，数据库服务关闭。

执行如下命令，更换yasdb IP：

```shell
# 更换host0001服务器上的yasdb IP
$ yasboot ipchange host -t hosts.toml -l 192.168.1.5 -r 192.168.1.5 --host-id host0001

# 更换host0002服务器上的yasdb IP
$ yasboot ipchange host -t hosts.toml -l 192.168.1.6 -r 192.168.1.6 --host-id host0002
```

## 步骤5：启动数据库

执行如下命令启动数据库服务：

```shell
$ yasboot cluster start -c yashandb
```

## 步骤6：检查IP更换结果

执行如下命令查询数据库集群的各项信息，校验IP是否已更换：

```shell
$ yasboot cluster status -c yashandb -d
+-------------------------------------------------------------------------------------------------------------------------------------------------+
| hostid   | node_type | nodeid | pid    | instance_status | database_status | database_role | listen_address   | data_path                       |
+-------------------------------------------------------------------------------------------------------------------------------------------------+
| host0001 | db        | 1-1:1  | 114864 | open            | normal          | standby       | 192.168.1.5:1688 | /data/yashan/yasdb_data/db-1-1  |
+----------+-----------+--------+--------+-----------------+-----------------+---------------+------------------+---------------------------------+
| host0002 | db        | 1-2:2  | 114862 | open            | normal          | standby       | 192.168.1.6:1688 | /data/yashan/yasdb_data/db-1-2  |
+----------+-----------+--------+--------+-----------------+-----------------+---------------+------------------+---------------------------------+
| host0003 | db        | 1-3:3  | 6921   | open            | normal          | primary       | 192.168.1.4:1688 | /data/yashan/yasdb_data/db-1-3  |
+----------+-----------+--------+--------+-----------------+-----------------+---------------+------------------+---------------------------------+
```
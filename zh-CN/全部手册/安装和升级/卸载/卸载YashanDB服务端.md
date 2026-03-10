本文介绍使用[yasboot](../../工具手册/yasboot/yasboot命令介绍/yasboot cluster)命令卸载YashanDB服务端的过程，相关操作需在对应服务器的安装路径（本文示例为安装用户yashan的install目录，即/home/yashan/install）下执行。

## 步骤1：检查并关闭yasom仲裁选主

若为单机一主一备部署或存算一体分布式高可用部署（DN组内节点为一主一备），需要执行当前操作检查并关闭yasom仲裁选主再执行后续操作。

1. 执行如下命令，检查yasom仲裁选主是否开启：

    ```shell
    $ yasboot election config show -c yashandb
    group 1
      Protection Mode: MAXIMUM PROTECTION
      Members:
        [1-1:1] - Primary database
          [1-2:2] - Physical standby database

    ……

    Automatic Failover: Enabled in Zero Data Loss Mode # Enabled表示已开启yasom仲裁选主
    ```

2. 若已开启，需执行如下命令关闭yasom仲裁选主：

    ```shell
    $ yasboot election enable off -c yashandb
    ```

## 步骤2： 卸载数据库

```shell
$ yasboot cluster clean --cluster yashandb --purge
```

## 步骤3：卸载yasom与yasagent

```shell
$ yasboot package uninstall --cluster yashandb
```

> **Note**:
>
> 若启用过[资源管理](../../数据库管理/资源管理/00资源管理)的CPU管理功能，本命令中须使用`-t`参数才能清理CPU资源管理功能的开机自启动配置。

## 步骤4：清理环境

- 清除环境变量：清除每个服务器上~/.bashrc中与YashanDB相关的[环境变量](../安装部署/安装后初始环境/环境变量)。

- 清除残留配置：若配置了开机自启动，需要手动[关闭](../安装部署/安装后初始环境/配置开机自启动.html#disable)。

- 清理配置文件（或移至其他路径进行归档）：建议归档或清理安装路径下的\<集群名>.toml和hosts.toml文件，否则在原服务器同一路径下再次部署同名共享集群时可能会出现YAS-05528报错。

卸载23.4.x.x及以上版本的共享集群时，还需额外执行以下清理操作。

- 清理残留进程：在每个服务器上执行如下命令清理ycsrootagent进程。

  ```shell
  $ sudo pkill -9 ycsrootagent
  ```

- 清理残留VIP：如果规划了[VIP](../安装部署/安装前准备/服务器准备.html#vip_planning)，需在每个服务器上执行如下命令清理VIP。

  ```shell
  # IP地址、子网掩码以及网卡名称均为示例值，请替换为实际值

  # 1. 获取网卡信息，在显示结果中找到VIP所在网卡的名称
  $ ip addr show
  2: ens192: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
      inet 192.168.1.62/24 scope global ens192
        valid_lft forever preferred_lft forever

  # 2. 清理残留VIP
  $ sudo ip addr del 192.168.1.62/24 dev ens192
  ```


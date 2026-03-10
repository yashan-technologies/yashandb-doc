YashanDB supports online scaling up/down standby clusters in YAC Deployment through [*yasboot*](../../../工具手册/yasboot/00yasboot), and the scaling does not affect the usage of the entire cluster.

> **Note**: 
>
> If the scaling up/down fails, please refer to [Scaling Issues Handling](../Scaling Issues Handling) for resolution.

## Scaling Up Standby Cluster Online

Each instance of the new standby cluster is hosted on a single separate server (referred to as the "new server" hereinafter). It is recommended that the operating system version of the new server be consistent with that of the existing servers.

1. Prepare the new servers and storage devices required for expansion, and refer to [Pre-installation Preparation](../../../安装和升级/安装部署/安装前准备/00安装前准备) to check and ensure that all environmental requirements of the new server system have been met.

2. Log in to the server where an existing node of the database is located as the installation user.

3. Execute the [yasboot config group gen](../../../工具手册/yasboot/yasboot命令介绍/yasboot config) command to generate the configuration file for adding standby cluster.

    ```shell
    $ yasboot config group gen -c yashandb \
    -u yashan -p yashan_password --ip ip1 --port 22 \
    --install-path /data/yashan/yasdb_home \
    --data-path /data/yashan/yasdb_data \
    --node 3 --begin-port 1688 --data /dev/yfs33/datadisk3 \
    --disk-found-path  /dev/yfs33 --system-data  /dev/yfs33/ycsdisk3 \
    --log-path /data/yashan/yasdb_data/log \
    -t ce \
    ```
    
    Upon successful execution, two configuration files will be generated: [yashandb_add.toml](../../../工具手册/yasboot/配置文件/集群扩容配置文件) and [hosts_add.toml](../../../工具手册/yasboot/配置文件/服务器扩容配置文件). 

4. Execute the following command to remotely connect to the new server(s) and install YashanDB on them.

    ```shell
    $ yasboot host add -c yashandb -t hosts_add.toml
    ```

5. On the server where the configuration file was generated, execute the following command to add standby cluster(s).

    ``` shell    
    $ yasboot group add -c yashandb -t yashandb_add.toml -p password
    ```

6. (Optional) Backup the database.

    It is recommended to perform a [backup](../../../数据库管理/备份与恢复/00备份与恢复) of the database to ensure there is a baseline backup set available for recovery after scaling.

7. Copy the [[host]] content from hosts_add.toml to the end of hosts.toml to avoid using old host information during an upgrade.

8. If all other servers have been configured for auto-start before the expansion, Log in to all new servers to be [configured for auto-start](../../安装部署/安装后初始环境/配置开机自启动).

## Scaling Down Standby Cluster Online

1. Log in to the database installation server using the installation user.


2. Execute the following command to view the ID of the target standby cluster to be deleted.

    ```shell
    $ yasboot cluster status -c yashandb -d
    # The part before the hyphen in nodeid is the cluster group ID; for example, for 2-1:3, the cluster group ID is 2
    ```

3. If the current environment is a primary-standby cluster, first check and turn off the yasom arbitration for master selection:

    ```shell
    $ yasboot election config show -c yashandb
    cluster
    Protection Mode: MAXIMUM PROTECTION
    Members:
        [1-1:1] - Primary database
        [1-2:2] - Primary database
        [2-1:3] - Physical standby database
                    Transport Lag: 0 seconds
                    Apply Lag:     0 seconds
                    Apply Rate:    0.00 KByte/s
        [2-2:4] - Physical standby database

    ……

    Automatic Failover: Enabled in Zero Data Loss Mode 

    # Enabled indicates that yasom election is enabled; disable it before proceeding to the next operation
    $ yasboot election enable off -c yashandb
    ```

4. Execute the following command to delete the standby cluster.

    ``` shell
    # You can add the --with-host parameter to continue deleting information of all empty servers without databases and without yasom processes in the current distributed cluster after successfully deleting the node group.
     $ yasboot group remove --group-ids 2 -f --purge --ce -c yashandb --with-host
    ```

5. (Optional) Backup the database.
    
    It is recommended to perform a [backup](../../../数据库管理/备份与恢复/00备份与恢复) of the database to ensure that a baseline backup set is available for recovery after scaling down.

6. If the --with-host parameter was specified during scaling down (i.e., empty servers were removed), after scaling down is complete, you need to update the [[host]] content of the hosts.toml file to delete the corresponding server information to avoid using old hosts information during upgrades.

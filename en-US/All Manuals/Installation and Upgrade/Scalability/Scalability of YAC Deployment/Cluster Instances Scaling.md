YashanDB supports online scaling up/down of instance nodes in YAC Deployment (including YCS instances and database instances) through the [*yasboot*](../../../工具手册/yasboot/00yasboot) tool, and the scaling up/down does not affect the usage of the entire cluster.

> **Note**: 
>
> Before performing the scaling operations, please carefully read the relevant [Considerations for Scaling](../Considerations for Scaling).
>
> If the scaling up/down fails, please refer to [Scaling Issues Handling](../Scaling Issues Handling) for resolution.

## Scaling Up Instance Online

Each instance will be hosted by a separate server (hereinafter referred to as "new server"). It is recommended that the new server's OS version be consistent with existing server(s).

1. Prepare the new servers required for expansion, and refer to [Pre-installation Preparation](../../../安装和升级/安装部署/安装前准备/00安装前准备) to check and ensure that all environmental requirements of the new server system have been met.

2. Log in to the server where an existing instance of the database is located as the installation user. 

3. Execute the [yasboot config node gen](../../../工具手册/yasboot/yasboot命令介绍/yasboot config) command to generate the configuration file for adding standby instance(s).

    ```shell
    $ yasboot config node gen -c yashandb \
    -u yashan -p password --ip ip1,ip2 --port 22 \
    --install-path /data/yashan/yasdb_home \
    --data-path /data/yashan/yasdb_data \
    --log-path /data/yashan/log \
    --node 2 \
    --vips vip1,vip2        # If the original cluster has not configured VIP, the --vips option does not need to be specified
    ```
    Upon successful execution, two configuration files will be generated: [yashandb_add.toml](../../../工具手册/yasboot/配置文件/集群扩容配置文件) and [hosts_add.toml](../../../工具手册/yasboot/配置文件/服务器扩容配置文件).

4. Execute the following command to remotely connect to the new server(s) and install YashanDB on them.

    ```shell
    $ yasboot host add -c yashandb -t hosts_add.toml
    ```

5. Execute the following command to add instance(s).

    ``` shell
    $ yasboot node add -c yashandb -t yashandb_add.toml
    ```

    A successful task does not necessarily indicate that the scaling is completely successful; there may still be backend tasks completing data synchronization and other operations. You can check if all scaling-related tasks succeeded using the `task list` command.

    ```shell
    $ yasboot task list -c yashandb --search type=NodeAdd
    ```

6. Copy and paste the [[host]] content from hosts_add.toml to the end of hosts.toml to avoid using outdated hosts information during upgrades.

7. If all other servers have been configured for auto-start before the expansion, Log in to all new servers to be [configured for auto-start](../../安装部署/安装后初始环境/配置开机自启动).

## Scaling Down Instance Online

1. Log in to the database installation server as the installation user. 


2. Execute the following command to view the instance ID of the target to be deleted.

    ```shell
    $ yasboot cluster status -c yashandb -d
    # The part before the colon in nodeid is the node ID; for example, 1-1:1 corresponds to node ID 1-1
    ```

3. Execute the following command to delete the instance.

    - Scenario 1: Delete the normal instance(s)

      ```shell
      # Delete one instance per operation
      $ yasboot node remove -c yashandb -n 1-1 --purge
      +----------------------------------------------------------------------------------------------------+
      | type | uuid             | name       | hostid | index    | status  | return_code | progress | cost |
      +----------------------------------------------------------------------------------------------------+
      | task | 7c7d71db43810b33 | NodeRemove | -      | yashandb | SUCCESS | 0           | 100      | 5    |
      +------+------------------+------------+--------+----------+---------+-------------+----------+------+
      task completed, status: SUCCESS
      
      # Delete multiple instances, separate the instance IDs with commas
      $ yasboot node remove -c yashandb --node-ids 1-1,1-2 --purge
      +----------------------------------------------------------------------------------------------------+
      | type | uuid             | name       | hostid | index    | status  | return_code | progress | cost |
      +----------------------------------------------------------------------------------------------------+
      | task | 7c7d71db43810b33 | NodeRemove | -      | yashandb | SUCCESS | 0           | 100      | 5    |
      +------+------------------+------------+--------+----------+---------+-------------+----------+------+
      +----------------------------------------------------------------------------------------------------+
      | task | 7c7d71db43810b34 | NodeRemove | -      | yashandb | SUCCESS | 0           | 100      | 5    |
      +------+------------------+------------+--------+----------+---------+-------------+----------+------+
      task completed, status: SUCCESS
      ```

    - Scenario 2: Delete the disconnected instance(s)

      If the server where the instance to be deleted is located can no longer be connected, it is necessary to confirm that the YCS instance on this server has been shut down and specify the `--with-unconnected-host` parameter for scaling down.

      ```shell
      # 1. Verify that the YCS instance on the target server is stopped.
      $ ycsctl status

      # 2. Proceed with the scale-in operation if the YCS instance residing on the server of the instance to be removed is in the OFFLIN state. Otherwise, log in to the server and run ycsctl stop ycs to stop the YCS instance first.
    
      # 3. Perform the scaling - down operation
      $ yasboot node remove -c yashandb --node-id 1-3 -d --with-unconnected-host --purge 
      +----------------------------------------------------------------------------------------------------+
      | type | uuid             | name       | hostid | index    | status  | return_code | progress | cost |
      +----------------------------------------------------------------------------------------------------+
      | task | 7c7d71db43810b35 | NodeRemove | -      | yashandb | SUCCESS | 0           | 100      | 5    |
      +------+------------------+------------+--------+----------+---------+-------------+----------+------+
      task completed, status: SUCCESS
      ```

4. (Optional) Backup the database.
    
    It is recommended to perform a [backup](../../../数据库管理/备份与恢复/00备份与恢复) of the database to ensure there is a baseline backup set available for recovery after scaling down.

    > **Note**: 
    >
    > After scaling in a YAC Deployment, if restoring from a pre-scale-in backup (with more instances than the current cluster), execute the following command after recovery to clean up excess data files:
    > ```shell
    > $ yasboot node remove -c yashandb --ce-clean
    > ```

5. If the `--with-host` parameter was specified during the scaling down (i.e., empty servers were removed), after scaling completes, you need to update the [[host]] content in the hosts.toml file to remove the corresponding server-related information to avoid using old host information during an upgrade.

6. (Optional) If the `--with-host` parameter is specified during the scaling down (i.e., empty servers were removed), after scaling completes, you can cleaned up the environment of the removed server:

    1. ) Remove leftover paths, including $YASDB_DATA, $YASDB_HOME, log directories, and local tablespace directories.

    2. ) Remove any lingering processes, such as yasdb and yascs.

    3. ) Remove leftover scripts, such as auto-start scripts.

    4. ) Remove leftover environment variables associated with YashanDB.



YashanDB supports online scaling up/down standby databases. Please read the relevant [considerations](../Considerations for Scaling) carefully before executing specific operations.

> **Note**: 
>
> If any existing nodes encounter failures or issues during the scaling process, please refer to [scaling issues handling](../Scaling Issues Handling) for resolution.

## Scaling Up Standby Database Online

Each standby database will be hosted by a separate server (hereinafter referred to as "new server"). It is recommended that the new server's OS version be consistent with existing server(s).

1. Prepare the new servers required for expansion, and refer to [Pre-installation Preparation](../../../安装和升级/安装部署/安装前准备/00安装前准备) to check and ensure that all environmental requirements of the new server system have been met.

2. Log in to the server where an existing node of the database is located as the installation user.

3. Execute the [yasboot config node gen](../../../工具手册/yasboot/yasboot命令介绍/yasboot config) command to generate the configuration file for adding standby database(s).

    ```shell
    $ yasboot config node gen -c yashandb \
    -u yashan -p password --ip ip1,ip2 --port 22 \
    --install-path /data/yashan/yasdb_home \
    --data-path /data/yashan/yasdb_data \
    --log-path /data/yashan/log \
    --node 2
    ```
    
    After successful execution, two configuration files will be generated: [yashandb_add.toml](../../../工具手册/yasboot/配置文件/集群扩容配置文件) and [hosts_add.toml](../../../工具手册/yasboot/配置文件/服务器扩容配置文件). 

4. Execute the following command to remotely connect to the new server(s) and install YashanDB on them.

    ```shell
    $ yasboot host add -c yashandb -t hosts_add.toml
    ```

5. Complete the following configurations on the new server(s) as required.
    
    - If the database has enabled the [Resource Management](../../../数据库管理/资源管理/00资源管理) function before expansion, the [yasboot host cgroup](../../../工具手册/yasboot/yasboot命令介绍/yasboot host) command needs to be executed on the new servers to create the cgroup directory.

        ```shell
        $ yasboot host cgroup create -c yashandb --sudo-username root --sudo-password ****** --host-id host004
        ```

    - If the database has enabled the [key management](../../../产品安全/加密支持/存储加密/密钥管理) function and created encrypted objects before expansion, the new server must also create the wallet file storage path, copy the corresponding TDE folder and its files from other servers to this path, and configure the wallet root directory WALLET_ROOT parameter to this path.

    - If other servers had configured automatic startup before scaling, the new server must also [configure automatic startup](../../安装部署/安装后初始环境/配置开机自启动).

6. On the server where the configuration file was generated, execute the following command to add standby database(s).

    ``` shell
    # If the database has enabled key management before expansion, --wallet-password Your_keystore_password must be specified

    $ yasboot node add -c yashandb -t yashandb_add.toml
    ```

    A successful task does not necessarily indicate that the scaling is completely successful; there may still be backend tasks completing data synchronization and other operations. You can check if all scaling-related tasks succeeded using the `task list` command.

    ```shell
    $ yasboot task list -c yashandb --search type=NodeAdd
    ```

7. (Optional) Backup the database.

    It is recommended to perform a [backup](../../../数据库管理/备份恢复/00备份恢复) of the database to ensure there is a baseline backup set available for recovery after scaling.

8. Copy the [[host]] content from hosts_add.toml to the end of hosts.toml to avoid using old host information during an upgrade.

9. Optionally, enable the leader election functionality to ensure business continuity:

    - In a one-primary/one-standby environment: You can manually enable [yasom election](../../../高可用/自动选主配置/一主一备yasom仲裁选主) as needed.

    - In a one-primary/multi-standby (non-cascade backup) environment: *yasboot* will automatically enable [leader election](../../../高可用/自动选主配置/一主多备自动选主).

## Scaling Down Standby Database Online

1. Log in to the database installation server using the installation user.


2. Execute the following command to view the standby database ID of the target to be deleted.

    ```shell
    $ yasboot cluster status -c yashandb -d
    # The part before the colon in nodeid is the node ID; for example, 1-1:1 corresponds to node ID 1-1
    ```

3. First, check and disable yasom election:

    If it is a one-primary/one-standby environment, disable yasom election before proceeding. If it is a one-primary/multi-standby environment, it does not affect online scaling down whether the leader election is enabled or not.

    ```shell
    $ yasboot election config show -c yashandb
    group 1
    Protection Mode: MAXIMUM PROTECTION
    Members:
        [1-1:1] - Primary database
        [1-2:2] - Physical standby database

    ……

    Automatic Failover: Enabled in Zero Data Loss Mode 

    # Enabled indicates that yasom election is enabled; disable it before proceeding to the next operation
    $ yasboot election enable off -c yashandb
    ```

4. Execute the following command to delete the standby database.

    ::: tabs
    == Delete the normal standby database(s)

```shell
# You can add the --with-host parameter to remove empty servers that do not have database nodes and yasom processes in the current database environment after successfully deleting the standby database

# Delete one standby database per operation
$ yasboot node remove -c yashandb -n 1-1 --purge
+----------------------------------------------------------------------------------------------------+
| type | uuid             | name       | hostid | index    | status  | return_code | progress | cost |
+----------------------------------------------------------------------------------------------------+
| task | 7c7d71db43810b33 | NodeRemove | -      | yashandb | SUCCESS | 0           | 100      | 5    |
+------+------------------+------------+--------+----------+---------+-------------+----------+------+
task completed, status: SUCCESS

# Delete multiple standby databases; use commas to separate IDs
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

After deleting the standby database, it will stop that standby database.

    == Delete the disconnected standby database(s)

If the server where the standby database to be deleted is located can no longer be connected, the `--with-unconnected-host` parameter must be specified for scaling down.

```shell
$ yasboot node remove --node-ids 1-3 --purge --force -c yashandb --with-unconnected-host
+----------------------------------------------------------------------------------------------------+
| type | uuid             | name       | hostid | index    | status  | return_code | progress | cost |
+----------------------------------------------------------------------------------------------------+
| task | 7c7d71db43810b33 | NodeRemove | -      | yashandb | SUCCESS | 0           | 100      | 5    |
+------+------------------+------------+--------+----------+---------+-------------+----------+------+
task completed, status: SUCCESS
```

> **Note**:
>
> In this scenario, after the downsizing is completed, you need to manually clean up the environment of the removed servers:
>
>- Remove leftover paths, including $YASDB_DATA, $YASDB_HOME, log directories, and local tablespace directories.
>
>-  Remove any lingering processes, such as yasdb and ycs.
>- Remove leftover scripts, such as auto-start scripts.
>- Remove leftover environment variables associated with YashanDB.

    :::

5. (Optional) Backup the database.
    
    It is recommended to perform a [backup](../../../数据库管理/备份恢复/00备份恢复) of the database to ensure there is a baseline backup set available for recovery after scaling down.

6. If the `--with-host` parameter was specified during the scaling down (i.e., empty servers were removed), after scaling completes, you need to update the [[host]] content in the hosts.toml file to remove the corresponding server-related information to avoid using old host information during an upgrade.

7. After deleting the standby database, you can restore the leader election functionality configuration as needed based on the database high availability configuration:

    - In a one-primary/one-standby environment: You can enable [yasom election](../../../高可用/自动选主配置/一主一备yasom仲裁选主).

    - In a one-primary/multi-standby (non-cascade backup) environment: You can enable [leader election](../../../高可用/自动选主配置/一主多备自动选主).

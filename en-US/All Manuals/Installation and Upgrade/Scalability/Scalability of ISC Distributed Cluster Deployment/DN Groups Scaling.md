YashanDB supports scaling the DN group in ISC Distributed Cluster Deployment using the [yasboot](../../../Tools Guide/yasboot/00yasboot) tool. Please read the relevant [Considerations for Scaling](../Considerations for Scaling) carefully before performing specific operations.

> **Note**: 
>
> If there are any failures, such as existing node failures or scaling failures during the scaling process, please refer to [Scaling Issues Handling](../Scaling Issues Handling) for resolution.

## Adding DN Groups

1. If you need to add new server(s) and increase DN groups, first refer to [Pre-installation Preparation](../../安装部署/安装前准备/00安装前准备) to check and ensure that all environmental requirements of the new server system have been met.

2. Log in to the server where an existing node of the database is located as the installation user.

3. Based on the actual scenario, execute the corresponding operation.

    - Scenario 1: Add DN group(s) by adding new server(s)

        1. ) Execute the [yasboot config node gen](../../../工具手册/yasboot/yasboot命令介绍/yasboot config) command based on the actual scenario to generate [yashandb_add.toml](../../../工具手册/yasboot/配置文件/集群扩容配置文件) and [hosts_add.toml](../../../工具手册/yasboot/配置文件/服务器扩容配置文件) configuration files.

        ```shell
        $ yasboot config group gen -c yashandb -u yashan -p password --ip ip1,ip2 --port 22 \
        --install-path /data/yashan/yasdb_home --data-path /data/yashan/yasdb_data \
        --log-path /data/yashan/log --node 3 --group 2
        ```

        2. ) Execute the following command to remotely connect to the new server(s) and install YashanDB on them.

        ```shell
        $ yasboot host add -c yashandb -t hosts_add.toml
        ```

        >  **Note**: 
        >
        > Complete the following configurations on the **new server(s)** as required:
        >
        > - If the database has enabled the [Resource Management](../../../数据库管理/资源管理/00资源管理) function before expansion, the [yasboot host cgroup](../../../工具手册/yasboot/yasboot命令介绍/yasboot host) command needs to be executed on the new server(s) to create the cgroup directory.
        > 
        >```shell
        >$ yasboot host cgroup create -c yashandb --sudo-username root --sudo-password ****** --host-id host004
        >```
        > 
        > - If other servers were configured for auto-start before expansion, the new server(s) must also [configure auto-start](../../安装部署/安装后初始环境/配置开机自启动).
    - Scenario 2: Add DN group(s) on existing server(s)

        Execute the [yasboot config node gen](../../../工具手册/yasboot/yasboot命令介绍/yasboot config) command based on the actual scenario to generate the [yashandb_add.toml](../../../工具手册/yasboot/配置文件/集群扩容配置文件) configuration file.

        ```shell
        $ yasboot config group gen -c yashandb --host-ids host0001,host0002 --node 3 --group 2
        ```

    >  **Note**: 
    >
    > For DN groups of 3 nodes or more, the HA_ELECTION_ENABLED parameter is enabled by default. If you need to disable it, you can modify this parameter after the scaling is successful.

4. On the server where the above - mentioned configuration file was generated, execute the following command to add the DN group.

    ``` shell
    $ yasboot group add -c yashandb -t yashandb_add.toml
    ```

    The task being marked as successful does not indicate that all scaling tasks have completed successfully; there may still be some background tasks finishing data synchronization and other operations. You can check if all scaling-related tasks are successful by using the `task list` command.

5. (Optional) Backup the database.

    It is recommended to perform a [backup](../../../数据库管理/备份与恢复/00备份与恢复) of the database to ensure that a baseline backup set is available for recovery after scaling.

6. If scaling was done by adding servers, after scaling is complete, you need to copy and paste the [[host]] content from hosts_add.toml to the end of hosts.toml to avoid using old hosts information during upgrades.

7. You can optionally enable leader election functionality among the nodes in the DN group as needed to ensure business continuity:

    - In a one-primary/one-standby environment: You need to manually enable [yasom election](../../../高可用/自动选主配置/一主一备yasom仲裁选主).

    - In a one-primary/multi-standby environment: *yasboot* will automatically enable [leader election](../../../高可用/自动选主配置/一主多备自动选主).

## Deleting DN Groups

1. Log in to the database installation server using the installation user.



2. Execute the following command to check the target group IDs to be deleted.

    ```shell
    $ yasboot cluster status -c yashandb -d
    # The part before the hyphen in nodeid is the group ID; for example, for 1-1:1, the group ID is 1.
    ```

3. Execute the following command to delete the DN group.

    ```shell
    # You can add the --with-host parameter to continue deleting information of all empty servers without databases and without yasom processes in the current distributed cluster after successfully deleting the node group.
    $ yasboot group remove --group-ids 3,4 --purge --force -c yashandb
    ```

4. (Optional) Backup the database.
    
    It is recommended to perform a [backup](../../../数据库管理/备份与恢复/00备份与恢复) of the database to ensure that a baseline backup set is available for recovery after scaling down.

5. If the --with-host parameter was specified during scaling down (i.e., empty servers were removed), after scaling down is complete, you need to update the [[host]] content of the hosts.toml file to delete the corresponding server information to avoid using old hosts information during upgrades.

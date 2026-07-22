YashanDB supports scaling out or scaling in the standby nodes in the DN group or MN group of ISC Distributed Cluster Deployment via the [*yasboot*](../../../Tools Guide/yasboot/00yasboot) tool.

> **Note**: 
>
> - Each operation can only scale out or scale in the standby nodes of 1 group at a time.
>
> - Before performing scaling operations, please carefully read the [Considerations for Scaling](../Considerations for Scaling).
>
> - If any exceptions occur during scaling, such as node failures or scaling failures, please refer to [Exception Handling for Scaling](../Exception Handling for Scaling) for resolution.

## Online Adding Standby Nodes in the Group

1. If you need to add new server(s) and increase standby node(s) in MN or DN groups, first refer to [Pre-installation Preparation](../../Installation and Deployment/Pre-Installation Preparation/00Pre-Installation Preparation) to check and ensure that all environmental requirements of the new server system have been met.

2. Log in to the server where an existing node of the database is located as the installation user.

3. Execute the following command to view the group ID of the target group to be scaled.

    ```shell
    $ yasboot cluster status -c yashandb -d
    +-------------------------------------------------------------------------------------------------------------------------------------------------+
    | hostid   | node_type | nodeid | pid   | instance_status | database_status | database_role | listen_address   | data_path                        |
    +-------------------------------------------------------------------------------------------------------------------------------------------------+
    | host0001 | mn        | 1-1:1  | 56961 | open            | normal          | primary       | 192.168.1.2:1678 | /data/yashan/yasdb_data/mn-1-1   |
    |          +-----------+--------+-------+-----------------+-----------------+---------------+------------------+----------------------------------+
    |          | dn        | 3-2:7  | 57029 | open            | normal          | standby       | 192.168.1.2:1698 | /data/yashan/yasdb_data/dn-3-2   |
    +----------+-----------+--------+-------+-----------------+-----------------+---------------+------------------+----------------------------------+
    | host0002 | mn        | 1-2:2  | 57024 | open            | normal          | standby       | 192.168.1.3:1678 | /data/yashan/yasdb_data/mn-1-2   |
    |          +-----------+--------+-------+-----------------+-----------------+---------------+------------------+----------------------------------+
    |          | cn        | 2-1:4  | 57024 | open            | normal          | primary       | 192.168.1.3:1688 | /data/yashan/yasdb_data/cn-2-1   |
    +----------+-----------+--------+-------+-----------------+-----------------+---------------+------------------+----------------------------------+
    | host0003 | mn        | 1-3:3  | 57021 | open            | normal          | standby       | 192.168.1.4:1678 | /data/yashan/yasdb_data/mn-1-3   |
    |          +-----------+--------+-------+-----------------+-----------------+---------------+------------------+----------------------------------+
    |          | cn        | 2-2:5  | 57021 | open            | normal          | primary       | 192.168.1.4:1688 | /data/yashan/yasdb_data/cn-2-2   |
    |          +-----------+--------+-------+-----------------+-----------------+---------------+------------------+----------------------------------+
    |          | dn        | 3-1:6  | 57021 | open            | normal          | primary       | 192.168.1.4:1698 | /data/yashan/yasdb_data/dn-3-1   |
    +----------+-----------+--------+-------+-----------------+-----------------+---------------+------------------+----------------------------------+
    ```

   From the output, we can see that the group ID of the DN group is 3, and the group ID of the MN group is 1.

4. Based on the actual scenario, execute the corresponding operation.

    - Scenario 1: Add standby node(s) by adding new server(s)

        1. ) Execute the [yasboot config node gen](../../../Tools Guide/yasboot/Introduction to yasboot Command/yasboot config) command based on the actual scenario to generate [yashandb_add.toml](../../../Tools Guide/yasboot/Configuration Files/Database Scale-out Configuration File) and [hosts_add.toml](../../../Tools Guide/yasboot/Configuration Files/Server Scale-out Configuration File) configuration files.

        ```shell
        $ yasboot config node gen -c yashandb -u yashan -p password --ip ip1,ip2 --port 22 \
        --install-path /data/yashan/yasdb_home --data-path /data/yashan/yasdb_data \
        --log-path /data/yashan/log -g 3 --node 2
        ```

        2. ) Execute the following command to remotely connect to the new server(s) and install YashanDB on them.

        ```shell
        $ yasboot host add -c yashandb -t hosts_add.toml
        ```

        >  **Note**: 
        >
        > Complete the following configurations on the **new server(s)** as required:
        >
        > - If the database has enabled the [Resource Management](../../../Database Administration/Resource Management/00Resource Management) function before scaling out, the [yasboot host cgroup](../../../Tools Guide/yasboot/Introduction to yasboot Command/yasboot host) command needs to be executed on the new server(s) to create the cgroup directory.
        > 
        >```shell
        >$ yasboot host cgroup create -c yashandb --sudo-username root --sudo-password ****** --host-id host004
        >```
        > 
        > - If other servers were configured for auto-start before scaling out, the new server(s) must also [configure auto-start](../../Installation and Deployment/Initial Environment after Installation/Configuring Boot Autostart).


    - Scenario 2: Add standby node(s) on existing server(s)

        Execute the [yasboot config node gen](../../../Tools Guide/yasboot/Introduction to yasboot Command/yasboot config) command based on the actual scenario to generate the [yashandb_add.toml](../../../Tools Guide/yasboot/Configuration Files/Database Scale-out Configuration File) configuration file.

        ```shell
        $ yasboot config node gen -c yashandb --host-ids host0001,host0002 -g 3 --node 2
        ```

5. On the server where the above - mentioned configuration file was generated, execute the following command to add standby nodes.

    ``` shell
    $ yasboot node add -c yashandb -t yashandb_add.toml
    +-------------------------------------------------------------------------------------------------+
    | type | uuid             | name    | hostid | index    | status  | return_code | progress | cost |
    +-------------------------------------------------------------------------------------------------+
    | task | 834a8c23c37ca1f4 | NodeAdd | -      | yashandb | SUCCESS | 0           | 100      | 3    |
    +------+------------------+---------+--------+----------+---------+-------------+----------+------+
    task completed, status: SUCCESS
    ```

    A successful task does not mean that the scaling task has completed successfully; there may still be background tasks completing data synchronization and other operations. You can check the status of scaling-related tasks using the `task list` command.

    ```shell
    $ yasboot task list -c yashandb --search type=NodeAdd
    ```

6. (Optional) Backup the database.

    It is recommended to [backup](../../../Database Administration/Backup and Recovery/00Backup and Recovery) the database to ensure there is a baseline backup set available for recovery after scaling.

7. If scaling out is performed by adding new servers, after completion, the [[host]] content in hosts_add.toml needs to be copied and pasted to the end of hosts.toml to avoid using old host information during the upgrade.


8. Optionally enable leader election functionality within the DN group nodes as needed to ensure business continuity:

    - One-primary/one-standby environment: You need to manually enable [yasom election](../../../High Availability/Configuring Leader Election/Configuring yasom Election for One Primary and One Standby).

    - One-primary/multi-standby environment: *yasboot* will automatically enable [leader election](../../../High Availability/Configuring Leader Election/Configuring Leader Election for One Primary and Multi-Standby).

## Online Deleting Standby Nodes in the Group

1. Log in to the database installation server using the installation user.


2. Execute the following command to view the ID of the target node to be deleted.

    ```shell
    $ yasboot cluster status -c yashandb -d
    # The part before the colon in nodeid is the node ID; for example, the node ID corresponding to 1-1:1 is 1-1
    ```

3. Please check and turn off yasom election first:

    For a one-primary/one-standby environment, you need to perform current operations to check and turn off yasom election. For a one-primary/multi-standby environment, whether or not leader election is enabled does not affect online scaling in performed via *yasboot*.

    ```shell
    $ yasboot election config show -c yashandb
    group 1
    Protection Mode: MAXIMUM PROTECTION
    Members:
        [1-1:1] - Primary database
        [1-2:2] - Physical standby node

    ……

    Automatic Failover: Enabled in Zero Data Loss Mode 

    # Enabled indicates that yasom election is enabled; disable it before proceeding to the next operation
    $ yasboot election enable off -c yashandb
    ```

4. Execute the following command to delete the standby node(s).


    ::: tabs
    == Delete the normal standby node(s)

```shell
# You can add the --with-host parameter to remove empty servers that do not have database nodes and yasom processes in the current database environment after successfully deleting the standby node

# Delete one standby node per operation
$ yasboot node remove -c yashandb -n 1-1 --purge
+----------------------------------------------------------------------------------------------------+
| type | uuid             | name       | hostid | index    | status  | return_code | progress | cost |
+----------------------------------------------------------------------------------------------------+
| task | 7c7d71db43810b33 | NodeRemove | -      | yashandb | SUCCESS | 0           | 100      | 5    |
+------+------------------+------------+--------+----------+---------+-------------+----------+------+
task completed, status: SUCCESS

# Delete multiple standby nodes; use commas to separate IDs
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

After deleting the standby node, it will stop that standby node.

    == Delete the disconnected standby node(s)

If the server where the standby node to be deleted is located can no longer be connected, the `--with-unconnected-host` parameter must be specified for scaling in.

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
    
    It is recommended to [backup](../../../Database Administration/Backup and Recovery/00Backup and Recovery) the database to ensure there is a baseline backup set available for recovery after scaling in.

6. If the --with-host parameter is specified during scale-in (that is, empty servers are removed), update the [[host]] entries in the hosts.toml file after the scale-in operation completes by deleting the information of the corresponding servers. This prevents the upgrade process from using outdated host configuration.

7. After deleting the standby node, depending on the database high availability configuration, you can restore/re-enable leader election functionality to ensure business continuity:

    - One-primary/one-standby environment: You can enable [yasom election](../../../High Availability/Configuring Leader Election/Configuring yasom Election for One Primary and One Standby).

    - One-primary/multi-standby environment: You can enable [leader election](../../../High Availability/Configuring Leader Election/Configuring Leader Election for One Primary and Multi-Standby).

In Dual Rep-Group Primary-Standby Deployment, scaling out or scaling in the standby databases in the primary replication group and non-primary nodes in the standby replication group (i.e., cascade standbys) is supported.

> **Note**:
>
> - Each operation can only add standby node(s) to 1 group.
>
> - Before performing scaling operations, please carefully read the [Considerations for Scaling](../Considerations for Scaling).
>
> - If any exceptions occur during scaling, such as node failures or scaling failures, please refer to [Exception Handling for Scaling](../Exception Handling for Scaling) for resolution.

## Scaling Out Standby Nodes in the Group Online

Each standby database will be hosted by a separate server (hereinafter referred to as "new server"). It is recommended that the new server's OS version be consistent with existing server(s).

1. Prepare the new servers required for scaling out, and refer to [Pre-installation Preparation](../../../Installation and Upgrade/Installation and Deployment/Pre-Installation Preparation/00Pre-Installation Preparation) to check and ensure that all environmental requirements of the new server system have been met.

2. Log in to the server where an existing node of the database is located as the installation user.

3. Check and disable *yasom* election:

    ```shell
    $ yasboot election config show -c yashandb
    group 1
    Protection Mode: MAXIMUM PROTECTION
    Members:
        [1-1:1] - Primary database
        [1-2:2] - Physical standby database

    ……

    Automatic Failover: Enabled in Zero Data Loss Mode

    # Enabled indicates that *yasom* election is enabled; disable it before proceeding to the next operation
    $ yasboot election enable off -c yashandb
    ```

4. Execute the following command to view the target replication group ID for adding standby database(s).

    ```shell
    $ yasboot cluster status -c yashandb -d
    # The number before the hyphen in nodeid is the group ID; for example, 1-1:1 corresponds to group ID 1
    ```

5. Execute the [yasboot config node gen](../../../Tools Guide/yasboot/Introduction to yasboot Command/yasboot config) command to generate the configuration file for adding nodes.

    ```shell
    # In Dual Rep-Group Primary-Standby Deployment for scaling out, use -g parameter to specify the target replication group ID
    $ yasboot config node gen -c yashandb \
    -u yashan -p yashan_password --ip ip1,ip2 --port 22 \
    --install-path /data/yashan/yasdb_home \
    --data-path /data/yashan/yasdb_data \
    --log-path /data/yashan/log \
    --begin-port 1688 \
    -g 2 --node 2
    ```

    After successful execution, two configuration files will be generated: [yashandb_add.toml](../../../Tools Guide/yasboot/Configuration Files/Database Scale-out Configuration File) and [hosts_add.toml](../../../Tools Guide/yasboot/Configuration Files/Server Scale-out Configuration File).

6. Execute the following command to remotely connect to the new server(s) and install YashanDB on them.

    ```shell
    $ yasboot host add -c yashandb -t hosts_add.toml
    ```

7. Execute the following command to add standby node(s).

    ```shell
    # If the database has enabled key management before scaling out, --wallet-password Your_keystore_password must be specified

    $ yasboot node add -c yashandb -t yashandb_add.toml
    ```

    A successful task does not necessarily indicate that the scaling is completely successful; there may still be backend tasks completing data synchronization and other operations. You can check if all scaling-related tasks succeeded using the `task list` command.

    ```shell
    $ yasboot task list -c yashandb --search type=NodeAdd
    ```

9. (Optional) Backup the database.

    It is recommended to perform a [backup](../../../Database Administration/Backup and Recovery/00Backup and Recovery) of the database to ensure that there is a baseline backup set available for recovery after the scaling out.

10. Copy the [[host]] content from hosts_add.toml and paste it at the end of hosts.toml to avoid using old hosts information during upgrades.

11. If any configurations have been temporarily adjusted for successful scaling out (e.g., disabling yasom election), restore the configuration as needed after scaling out is complete (e.g., re-enable [yasom election](../../../High Availability/Configuring Leader Election/Configuring yasom Election)).

12. If all other servers have been configured for auto-start before the scaling out, the new server must also be [configured for auto-start](../../Installation and Deployment/Initial Environment after Installation/Configuring Boot Autostart).

## Scaling In Standby Nodes in the Group Online

In Dual Rep-Group Primary-Standby Deployment, the primary node (i.e., remote standby) in the standby replication group **cannot** be directly deleted. To delete it, first execute the yasboot node switch-remote-standby command to switch another node to the remote standby role, then delete the target node after it is demoted to a cascade standby.

1. Check and disable *yasom* election.

    ```shell
    $ yasboot election config show -c yashandb
    group 1
    Protection Mode: MAXIMUM PROTECTION
    Members:
        [1-1:1] - Primary database
        [1-2:2] - Physical standby database
    ……

    Automatic Failover: Enabled in Zero Data Loss Mode

    # Enabled indicates that *yasom* election is enabled; disable it before proceeding to the next operation
    $ yasboot election enable off -c yashandb
    ```

2. Execute the following command to view the standby database ID and its role of the target to be deleted.

    ```shell
    $ yasboot cluster status -c yashandb -d
    +----------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | hostid   | node_type | nodeid | pid   | instance_status | database_status | database_role | source_node | listen_address   | data_path                         |
    +----------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | host0001 | db        | 1-1:1  | 73132 | open            | normal          | primary       | -           | 192.168.1.2:1688  | /data/yashan/yasdb_data/db-1-1    |
    +----------+-----------+--------+-------+-----------------+-----------------+---------------+-------------+-------------------+-----------------------------------+
    | host0002 | db        | 1-2:2  | 73199 | open            | normal          | standby       | 1-1:1       | 192.168.1.3:1688  | /data/yashan/yasdb_data/db-1-2    |
    +----------+-----------+--------+-------+-----------------+-----------------+---------------+-------------+-------------------+-----------------------------------+
    | host0003 | db        | 1-3:3  | 73201 | open            | normal          | standby       | 1-1:1       | 192.168.1.4:1688  | /data/yashan/yasdb_data/db-1-3    |
    +----------+-----------+--------+-------+-----------------+-----------------+---------------+-------------+-------------------+-----------------------------------+
    | host0004 | db        | 2-1:4  | 73362 | open            | normal          | standby       | 1-1:1       | 192.168.10.5:1688 | /data/yashan/yasdb_data/db-2-1    |
    +----------+-----------+--------+-------+-----------------+-----------------+---------------+-------------+-------------------+-----------------------------------+
    | host0005 | db        | 2-2:5  | 73435 | open            | normal          | standby       | 2-1:4       | 192.168.10.6:1688 | /data/yashan/yasdb_data/db-2-2    |
    +----------+-----------+--------+-------+-----------------+-----------------+---------------+-------------+-------------------+-----------------------------------+
    | host0006 | db        | 2-3:6  | 73499 | open            | normal          | standby       | 2-1:4       | 192.168.10.7:1688 | /data/yashan/yasdb_data/db-2-3    |
    +----------+-----------+--------+-------+-----------------+-----------------+---------------+-------------+-------------------+-----------------------------------+
    ```

    - You can obtain the group ID and node ID through the nodeid field; for example, `2-3:3` corresponds to node ID 2-3, and its group ID is 2.

    - By combining the database_role and source_node fields, it can be determined that node 2-1 is the primary node (i.e., remote standby) of the standby replication group. To delete this node, you must first execute the yasboot node switch-remote-standby command to complete the role switch, demoting it to a non-primary node (i.e., cascade standby).

3. Execute the node scaling in command.

    - Scenario 1: Delete the normal node(s)

      ```shell
      $ yasboot node remove -c yashandb --node-ids 2-3 --purge -f
      +----------------------------------------------------------------------------------------------------+
      | type | uuid             | name       | hostid | index    | status  | return_code | progress | cost |
      +----------------------------------------------------------------------------------------------------+
      | task | 7c7d71db43810b33 | NodeRemove | -      | yashandb | SUCCESS | 0           | 100      | 5    |
      +------+------------------+------------+--------+----------+---------+-------------+----------+------+
      task completed, status: SUCCESS
      ```

      After deleting the node, it will stop that node.

    - Scenario 2: Delete the disconnected node(s)

      If the server where the node to be deleted is located can no longer be connected, the `--with-unconnected-host` parameter must be specified for scaling in.

      ```shell
      # The server will be forcibly deleted in this way, deleting only the data of yasom on the server, while the yasagent process and installation package will be retained.

      $ yasboot node remove --node-ids 2-3 --purge --force -c yashandb --with-unconnected-host
      +----------------------------------------------------------------------------------------------------+
      | type | uuid             | name       | hostid | index    | status  | return_code | progress | cost |
      +----------------------------------------------------------------------------------------------------+
      | task | 7c7d71db43810b33 | NodeRemove | -      | yashandb | SUCCESS | 0           | 100      | 5    |
      +------+------------------+------------+--------+----------+---------+-------------+----------+------+
      task completed, status: SUCCESS
      ```

      After deleting a node, it will not stop the node. If the node is monitored by monit, it will not terminate the process monitoring of that node.

4. (Optional) Backup the database.

    It is recommended to perform a [backup](../../../Database Administration/Backup and Recovery/00Backup and Recovery) of the database to ensure there is a baseline backup set available for recovery after scaling in.

5. If any configurations have been temporarily adjusted for successful scaling in, restore the configuration as needed after scaling in is complete.

6. (Optional) If the `--with-unconnected-host` parameter is specified during scale-in (i.e., instances that cannot be connected are deleted), you can clean up the environment for the servers that cannot be connected after the scale-in is complete:

    - Remove leftover paths, including $YASDB_DATA, $YASDB_HOME, log directories, and local tablespace directories.

    - Remove any lingering processes, such as yasdb.

    - Remove leftover scripts, such as auto-start scripts.

    - Remove leftover environment variables associated with YashanDB.


Cascade standby database is a physical standby database that receives logs from a certain physical standby database (called the "parent standby database"). YashanDB supports adding new cascade standby databases to an existing standby database and removing existing cascade standby databases.

> **Note**:
>
> - Before performing scaling operations, please carefully read the [Considerations for Scaling](../Considerations for Scaling).
>
> - If the number of parent standby databases in the current environment is 0 or 1, you can refer to this document to use yasboot to complete the addition or removal of cascade standby databases. If there are multiple parent standby databases in the current environment, please contact our technical support.
>
> - If any exceptions occur during scaling, such as node failures or scaling failures, please refer to [Exception Handling for Scaling](../Exception Handling for Scaling) for resolution.


##  Preparatory Operations

1. Log in to the server where an existing node of the database is located as the installation user.

2. Execute the following command to view the current environment configuration.

    ```shell
    $ yasboot cluster status -c yashandb -d
    +--------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | hostid   | node_type | nodeid | pid   | instance_status | database_status | database_role | listen_address    | source_node | data_path                      |
    +--------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | host0001 | db        | 1-1:1  | 25283 | open            | normal          | primary       | 192.168.1.2:1688  | -           | /data/yashan/yasdb_data/db-1-1 |
    +----------+-----------+--------+-------+-----------------+-----------------+---------------+-------------------+-------------+--------------------------------+
    | host0002 | db        | 1-2:2  | 31194 | open            | normal          | standby       | 192.168.1.3:1688  | 1-1:1       | /data/yashan/yasdb_data/db-1-2 |
    +----------+-----------+--------+-------+-----------------+-----------------+---------------+-------------------+-------------+--------------------------------+
    | host0003 | db        | 1-3:3  | 13384 | open            | normal          | standby       | 192.168.1.4:1688  | 1-1:1       | /data/yashan/yasdb_data/db-1-3 |
    +----------+-----------+--------+-------+-----------------+-----------------+---------------+-------------------+-------------+--------------------------------+
    ```

    The source_node field is used to identify the upper-level node of the current node (the primary database of the standby database or the parent standby database of the cascade standby):

    - If the value set of source_node only contains the primary database (nodes with database_role=primary), it means there is no cascade standby in the current environment, and therefore no parent standby database exists. In this case, you can use yasboot one-click to add a cascade standby to any one standby database.

    - If the value set of source_node is primary database + 1 standby database (nodes with database_role=standby), it means there is only 1 parent standby database in the current environment. In this case, you can use yasboot to add or remove cascade standby databases for that parent standby database. If you need to add cascade standby databases for other standby databases, please contact our technical support.

    - If the value set of source_node does not match either of the two cases above, it means there are already multiple parent standby databases in the current environment. If you need to add or remove cascade standby databases, please contact our technical support.

    At this time, it is recommended to record the nodeid of the target parent standby database that needs cascade standby addition or removal operations for subsequent operations.


## Scaling Out Cascade Standby Online

Each standby database will be hosted by a separate server (hereinafter referred to as "new server"). It is recommended that the new server's OS version be consistent with existing server(s).

1. Prepare the new servers required for scaling out, and refer to [Pre-installation Preparation](../../../Installation and Upgrade/Installation and Deployment/Pre-Installation Preparation/00Pre-Installation Preparation) to check and ensure that all environmental requirements of the new server system have been met.

2. Log in to the server where an existing node of the database is located as the installation user.

3. Check and disable yasom election:

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

4. Execute the following command to view the target standby database ID for adding cascade standby(s).

    ```shell
    $ yasboot cluster status -c yashandb -d
    # The part before the colon in nodeid is the node ID; for example, 1-2:2 corresponds to node ID 1-2
    ```
5. Execute the [yasboot config node gen](../../../Tools Guide/yasboot/Introduction to yasboot Command/yasboot config) command to generate the configuration file for adding standby database(s).

    ```shell
    # If there is already 1 parent standby database in the current environment, the --cascade-parent parameter can only be specified as that parent standby database
    $ yasboot config node gen -c yashandb \
    -u yashan -p yashan_password --ip ip1,ip2 --port 22 \
    --install-path /data/yashan/yasdb_home \
    --data-path /data/yashan/yasdb_data \
    --log-path /data/yashan/log \
    --cascade-node 2 --cascade-parent 1-2
    ```

    After successful execution, two configuration files will be generated: [yashandb_add.toml](../../../Tools Guide/yasboot/Configuration Files/Database Scale-out Configuration File) and [hosts_add.toml](../../../Tools Guide/yasboot/Configuration Files/Server Scale-out Configuration File).

6. Execute the following command to remotely connect to the new server(s) and install YashanDB on them.

    ```shell
    $ yasboot host add -c yashandb -t hosts_add.toml
    ```

7. Execute the following command to add standby database(s).

    ```shell
    # If the database has enabled key management before scaling out, --wallet-password Your_keystore_password must be specified

    $ yasboot node add -c yashandb -t yashandb_add.toml
    ```

    The task being marked as successful does not mean that all scaling out tasks have been fully completed; some background tasks may still be synchronizing data and performing other operations. You can check whether all tasks related to scaling out are successful by using the `task list` command.

    ```shell
    $ yasboot task list -c yashandb --search type=NodeAdd
    ```

8. (Optional) Backup the database.

    It is recommended to perform a [backup](../../../Database Administration/Backup and Recovery/00Backup and Recovery) of the database to ensure that there is a baseline backup set available for recovery after the scaling out.

9. Copy the [[host]] content from hosts_add.toml and paste it at the end of hosts.toml to avoid using old hosts information during upgrades.

10. If any configurations have been temporarily adjusted for successful scaling out (e.g., disabling yasom election), restore the configuration as needed after scaling out is complete (e.g., re-enable [yasom election](../../../High Availability/Configuring Leader Election/Configuring yasom Election)).

11. If all other servers have been configured for auto-start before the scaling out, the new server must also be [configured for auto-start](../../Installation and Deployment/Initial Environment after Installation/Configuring Boot Autostart).

## Scaling In Cascade Standby Online

1. Log in to the database installation server using the installation user.


2. Check and disable yasom election:

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

3. Execute the following command to view the cascade standby database ID of the target to be deleted.

    ```shell
    $ yasboot cluster status -c yashandb -d
    # The part before the colon in nodeid is the node ID; for example, 1-5:5 corresponds to node ID 1-5
    ```

4. Execute the following command to delete cascade standby database(s).


    - Scenario 1: Delete the normal cascade standby database(s)

      ```shell
      # Delete one cascade standby database per operation
      $ yasboot node remove -c yashandb -n 1-5 --purge -f
      +----------------------------------------------------------------------------------------------------+
      | type | uuid             | name       | hostid | index    | status  | return_code | progress | cost |
      +----------------------------------------------------------------------------------------------------+
      | task | 7c7d71db43810b33 | NodeRemove | -      | yashandb | SUCCESS | 0           | 100      | 5    |
      +------+------------------+------------+--------+----------+---------+-------------+----------+------+
      task completed, status: SUCCESS

      # Delete multiple cascade standby databases; use commas to separate IDs
      $ yasboot node remove -c yashandb --node-ids 1-5,1-6 --purge
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

      After deleting the cascade standby database, it will stop that cascade standby database.

    - Scenario 2: Delete the disconnected cascade standby database(s)

      If the server where the cascade standby database to be deleted is located can no longer be connected, the `--with-unconnected-host` parameter must be specified for scaling in.

      ```shell
      # The server will be forcibly deleted in this way, deleting only the data of yasom on the server, while the yasagent process and installation package will be retained.

      $ yasboot node remove --node-ids 1-5 --purge --force -c yashandb --with-unconnected-host
      +----------------------------------------------------------------------------------------------------+
      | type | uuid             | name       | hostid | index    | status  | return_code | progress | cost |
      +----------------------------------------------------------------------------------------------------+
      | task | 7c7d71db43810b33 | NodeRemove | -      | yashandb | SUCCESS | 0           | 100      | 5    |
      +------+------------------+------------+--------+----------+---------+-------------+----------+------+
      task completed, status: SUCCESS
      ```

      After deleting a cascade standby database, it will not stop that cascade standby database. If the cascade standby database is monitored by monit, it will not terminate the process monitoring of that cascade standby database.

5. (Optional) Backup the database.

    It is recommended to perform a [backup](../../../Database Administration/Backup and Recovery/00Backup and Recovery) of the database to ensure there is a baseline backup set available for recovery after scaling in.

6. If any configurations have been temporarily adjusted for successful scaling in, restore the configuration as needed after scaling in is complete.

7. (Optional) If the `--with-unconnected-host` parameter is specified during scale-in (i.e., instances that cannot be connected are deleted), you can clean up the environment for the servers that cannot be connected after the scale-in is complete:

    - Remove leftover paths, including $YASDB_DATA, $YASDB_HOME, log directories, and local tablespace directories.

    - Remove any lingering processes, such as yasdb.

    - Remove leftover scripts, such as auto-start scripts.

    - Remove leftover environment variables associated with YashanDB.

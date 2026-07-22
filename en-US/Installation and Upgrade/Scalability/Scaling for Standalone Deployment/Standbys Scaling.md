YashanDB supports online scaling out or scaling in standby databases. 

> **Note**: 
>
> - Before performing scaling operations, please carefully read the [Considerations for Scaling](../Considerations for Scaling).
>
> - If any exceptions occur during scaling, such as node failures or scaling failures, please refer to [Exception Handling for Scaling](../Exception Handling for Scaling) for resolution.

## Scaling out Standby Database Online

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

4. Execute the [yasboot config node gen](../../../Tools Guide/yasboot/Introduction to yasboot Command/yasboot config) command to generate the configuration file for adding standby database(s).

    ```shell
    $ yasboot config node gen -c yashandb \
    -u yashan -p yashan_password --ip ip1,ip2 --port 22 \
    --install-path /data/yashan/yasdb_home \
    --data-path /data/yashan/yasdb_data \
    --log-path /data/yashan/log \
    --node 2
    ```
    
    After successful execution, two configuration files will be generated: [yashandb_add.toml](../../../Tools Guide/yasboot/Configuration Files/Database Scale-out Configuration File) and [hosts_add.toml](../../../Tools Guide/yasboot/Configuration Files/Server Scale-out Configuration File). 

5. Execute the following command to remotely connect to the new server(s) and install YashanDB on them.

    ```shell
    $ yasboot host add -c yashandb -t hosts_add.toml
    ```

6. Complete the following configurations on the new server(s) as required.

    - If the database has enabled the [Resource Management](../../../Database Administration/Resource Management/00Resource Management) function before scaling out, the [yasboot host cgroup](../../../Tools Guide/yasboot/Introduction to yasboot Command/yasboot host) command needs to be executed on the new servers to create the cgroup directory.

        ```shell
        $ yasboot host cgroup create -c yashandb --sudo-username root --sudo-password ****** --host-id host004
        ```

    - If all other servers have been configured for auto-start before the scaling out, the new server must also be [configured for auto-start](../../Installation and Deployment/Initial Environment after Installation/Configuring Boot Autostart).

7. On the server where the configuration file was generated, execute the following command to add standby database(s).

    ``` shell
    # If the database has enabled key management before scaling out, --wallet-password Your_keystore_password must be specified

    $ yasboot node add -c yashandb -t yashandb_add.toml
    ```

    A successful task does not necessarily indicate that the scaling is completely successful; there may still be backend tasks completing data synchronization and other operations. You can check if all scaling-related tasks succeeded using the `task list` command.

    ```shell
    $ yasboot task list -c yashandb --search type=NodeAdd
    ```

8. (Optional) Backup the database.

    It is recommended to perform a [backup](../../../Database Administration/Backup and Recovery/00Backup and Recovery) of the database to ensure there is a baseline backup set available for recovery after scaling.

9. Copy the [[host]] content from hosts_add.toml to the end of hosts.toml to avoid using old host information during an upgrade.

10. Optionally, enable the leader election functionality to ensure business continuity:

    - In a Standalone Primary-Standby Deployment (non-cascade standby) environment: You can manually enable [yasom election](../../../High Availability/Configuring Leader Election/Configuring yasom Election) as needed.

    - In a one-primary/multi-standby (non-cascade standby) environment: *yasboot* will automatically enable [leader election](../../../High Availability/Configuring Leader Election/Configuring Leader Election for One Primary and Multi-Standby).

## Scaling in Standby Database Online

1. Log in to the database installation server using the installation user.


2. Check and disable yasom election:

    ```shell
    $ yasboot election config show -c yashandb
    group 1
    Protection Mode: MAXIMUM PROTECTION
    Members:
        [1-1:1] - Primary database
        [1-2:2] - Physical standby database    
        [1-3:3] - Physical standby database

    ……

    Automatic Failover: Enabled in Zero Data Loss Mode 

    # Enabled indicates that yasom election is enabled; disable it before proceeding to the next operation
    $ yasboot election enable off -c yashandb
    ```

3. Execute the following command to view the standby database ID of the target to be deleted.

    ```shell
    $ yasboot cluster status -c yashandb -d
    # The part before the colon in nodeid is the node ID; for example, 1-3:3 corresponds to node ID 1-3
    ```
    
    **Not allowed** to delete a standby database that has cascade standbys. To delete it, you must first refer to [Cascading Standbys Scaling](./Cascading Standbys Scaling) to delete all its cascade standbys.

4. Execute the following command to delete the standby database.

    - Scenario 1: Delete the normal standby database(s)

      ```shell
      # Delete one standby database per operation
      $ yasboot node remove -c yashandb -n 1-3 --purge
      +----------------------------------------------------------------------------------------------------+
      | type | uuid             | name       | hostid | index    | status  | return_code | progress | cost |
      +----------------------------------------------------------------------------------------------------+
      | task | 7c7d71db43810b33 | NodeRemove | -      | yashandb | SUCCESS | 0           | 100      | 5    |
      +------+------------------+------------+--------+----------+---------+-------------+----------+------+
      task completed, status: SUCCESS

      # Delete multiple standby databases; use commas to separate IDs
      $ yasboot node remove -c yashandb --node-ids 1-3,1-2 --purge
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

    - Scenario 2: Delete the disconnected standby database(s)

      If the server where the standby database to be deleted is located can no longer be connected, the `--with-unconnected-host` parameter must be specified for scaling in.

      ```shell
      # The server will be forcibly deleted in this way, deleting only the data of yasom on the server, while the yasagent process and installation package will be retained.

      $ yasboot node remove --node-ids 1-3 --purge --force -c yashandb --with-unconnected-host
      +----------------------------------------------------------------------------------------------------+
      | type | uuid             | name       | hostid | index    | status  | return_code | progress | cost |
      +----------------------------------------------------------------------------------------------------+
      | task | 7c7d71db43810b33 | NodeRemove | -      | yashandb | SUCCESS | 0           | 100      | 5    |
      +------+------------------+------------+--------+----------+---------+-------------+----------+------+
      task completed, status: SUCCESS
      ```

      After deleting a standby database, it will not stop the standby database. If the standby database is monitored by monit, it will not terminate the monitoring process of the standby database.

5. (Optional) Backup the database.
    
    It is recommended to perform a [backup](../../../Database Administration/Backup and Recovery/00Backup and Recovery) of the database to ensure there is a baseline backup set available for recovery after scaling in.

6. If the --with-host parameter is specified during scale-in (that is, empty servers are removed), update the [[host]] entries in the hosts.toml file after the scale-in operation completes by deleting the information of the corresponding servers. This prevents the upgrade process from using outdated host configuration.

7. After deleting the standby database, you can enable the leader election functionality configuration as needed based on the database high availability configuration:

    - In a Standalone Primary-Standby Deployment (non-cascade standby) environment: You can manually enable [yasom election](../../../High Availability/Configuring Leader Election/Configuring yasom Election) as needed.

    - In a one-primary/multi-standby (non-cascade standby) environment: You can enable [leader election](../../../High Availability/Configuring Leader Election/Configuring Leader Election for One Primary and Multi-Standby).

8. (Optional) If the `--with-unconnected-host` parameter is specified during scale-in (i.e., instances that cannot be connected are deleted), you can clean up the environment for the servers that cannot be connected after the scale-in is complete:

    - Remove leftover paths, including $YASDB_DATA, $YASDB_HOME, log directories, and local tablespace directories.

    - Remove any lingering processes, such as yasdb.

    - Remove leftover scripts, such as auto-start scripts.

    - Remove leftover environment variables associated with YashanDB.

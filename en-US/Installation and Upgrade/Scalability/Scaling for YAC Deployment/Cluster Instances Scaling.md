YashanDB supports online scale-out or scale-in of instance nodes in YAC Deployment (including YCS instances and database instances) through the [*yasboot*](../../../Tools Guide/yasboot/00yasboot) tool, and the scale-out or scale-in does not affect the usage of the entire cluster.

> **Note**: 
>
> - Before performing scaling operations, please carefully read the [Considerations for Scaling](../Considerations for Scaling).
>
> - If scaling operations fail, please refer to [Exception Handling for Scaling](../Exception Handling for Scaling) for resolution.

## scaling out Instance Online

Each instance will be hosted by a separate server (hereinafter referred to as "new server"). It is recommended that the new server's OS version be consistent with existing server(s).

1. Prepare the new servers required for scaling out, and refer to [Pre-installation Preparation](../../../Installation and Upgrade/Installation and Deployment/Pre-Installation Preparation/00Pre-Installation Preparation) to check and ensure that all environmental requirements of the new server system have been met.

2. Log in to the server where an existing instance of the database is located as the installation user. 

3. Check and disable yasom election:

    If it is a one-primary/one-standby environment, disable yasom election before proceeding. If it is a one-primary/multi-standby environment, it does not affect online scale-in whether the leader election is enabled or not.

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

4. Execute the [yasboot config node gen](../../../Tools Guide/yasboot/Introduction to yasboot Command/yasboot config) command to generate the configuration file for adding standby instance(s).

    ```shell
    $ yasboot config node gen -c yashandb \
    -u yashan -p password --ip ip1,ip2 --port 22 \
    --install-path /data/yashan/yasdb_home \
    --data-path /data/yashan/yasdb_data \
    --log-path /data/yashan/log \
    --node 2 \
    --vips vip1,vip2        # If the original cluster has not configured VIP, the --vips option does not need to be specified
    ```
    Upon successful execution, two configuration files will be generated: [yashandb_add.toml](../../../Tools Guide/yasboot/Configuration Files/Database Scale-out Configuration File) and [hosts_add.toml](../../../Tools Guide/yasboot/Configuration Files/Server Scale-out Configuration File).

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


7. On the server where the configuration file was generated, execute the following command to add instance(s).

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

10. If any configurations have been temporarily adjusted for successful scaling out (e.g., disabling yasom election), restore the configuration as needed after scaling out is complete (e.g., re-enable [yasom election](../../../High Availability/Configuring Leader Election/Configuring yasom Election)).

## Scaling in Instance Online

1. Log in to the database installation server as the installation user. 


2. Check and disable yasom election:

    If it is a one-primary/one-standby environment, disable yasom election before proceeding. If it is a one-primary/multi-standby environment, it does not affect online scale-in whether the leader election is enabled or not.

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

3. Execute the following command to view the instance ID of the target to be deleted.

    ```shell
    $ yasboot cluster status -c yashandb -d
    # The part before the colon in nodeid is the node ID; for example, 1-1:1 corresponds to node ID 1-1
    ```

4. Execute the following command to delete the instance.

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

      If the server where the instance to be deleted is located can no longer be connected, it is necessary to confirm that the YCS instance on this server has been shut down and specify the `--with-unconnected-host` parameter for scale-in.

      ```shell
      # 1. Verify that the YCS instance on the target server is stopped.
      $ ycsctl status

      # 2. Proceed with the scale-in operation if the YCS instance residing on the server of the instance to be removed is in the OFFLIN state. Otherwise, log in to the server and run ycsctl stop ycs to stop the YCS instance first.
    
      # 3. Perform Scale-In
      $ yasboot node remove -c yashandb --node-id 1-3 -d --with-unconnected-host --purge 
      +----------------------------------------------------------------------------------------------------+
      | type | uuid             | name       | hostid | index    | status  | return_code | progress | cost |
      +----------------------------------------------------------------------------------------------------+
      | task | 7c7d71db43810b35 | NodeRemove | -      | yashandb | SUCCESS | 0           | 100      | 5    |
      +------+------------------+------------+--------+----------+---------+-------------+----------+------+
      task completed, status: SUCCESS
      ```

5. (Optional) Backup the database.
    
    It is recommended to perform a [backup](../../../Database Administration/Backup and Recovery/00Backup and Recovery) of the database to ensure there is a baseline backup set available for recovery after scale-in.

    > **Note**: 
    >
    > After scale-in a YAC Deployment, if restoring from a pre-scale-in backup (with more instances than the current cluster), execute the following command after recovery to clean up excess data files:
    > ```shell
    > $ yasboot node remove -c yashandb --ce-clean
    > ```

6. If the --with-host parameter is specified during scale-in (that is, empty servers are removed), update the [[host]] entries in the hosts.toml file after the scale-in operation completes by deleting the information of the corresponding servers. This prevents the upgrade process from using outdated host configuration.

7. If any configurations have been temporarily adjusted for successful scale-in (e.g., disabling yasom election), restore the configuration as needed after scale-in is complete (e.g., re-enable [yasom election](../../../High Availability/Configuring Leader Election/Configuring yasom Election)).

8. (Optional) If the `--with-host` parameter is specified during the scale-in (i.e., empty servers were removed), after scaling completes, you can cleaned up the environment of the removed server:

    1. ) Remove leftover paths, including $YASDB_DATA, $YASDB_HOME, log directories, and local tablespace directories.

    2. ) Remove any lingering processes, such as yasdb and yascs.

    3. ) Remove leftover scripts, such as auto-start scripts.

    4. ) Remove leftover environment variables associated with YashanDB.



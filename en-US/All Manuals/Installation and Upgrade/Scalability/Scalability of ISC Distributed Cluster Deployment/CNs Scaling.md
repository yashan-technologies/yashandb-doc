YashanDB supports scaling up or down CN nodes in ISC Distributed Cluster Deployment via the [yasboot](../../../Tools Guide/yasboot/00yasboot) tool. Before executing specific actions, please read the related [considerations](../Considerations for Scaling) carefully.

> **Note**: 
>
> If there are any issues such as node failures or scaling failures during the process, please refer to [scaling issues handling](../Scaling Issues Handling) for resolution.

## Adding CN Nodes

1. If you need to add new server(s) and increase CN nodes, first refer to [Pre-installation Preparation](../../Installation and Deployment/Pre-Installation Preparation/00Pre-Installation Preparation) to check and ensure that all environmental requirements of the new server system have been met.

2. Log in to the server where an existing node of the database is located as the installation user.

3. Based on the actual scenario, execute the corresponding operation.

    - Scenario 1: Add CN node(s) by adding new server(s)

        1. ) Execute the [yasboot config node gen](../../../Tools Guide/yasboot/Introduction to yasboot Command/yasboot config) command based on the actual scenario to generate [yashandb_add.toml](../../../Tools Guide/yasboot/Configuration Files/Database Scaling Configuration File) and [hosts_add.toml](../../../Tools Guide/yasboot/Configuration Files/Server Scaling Configuration File) configuration files.

        ```shell
        $ yasboot config node gen -c yashandb -u yashan -p password --ip ip1,ip2 --port 22 \
        --install-path /data/yashan/yasdb_home --data-path /data/yashan/yasdb_data \
        --log-path /data/yashan/log -g 2 --node 2
        ```

        2. ) Execute the following command to remotely connect to the new server(s) and install YashanDB on them.

        ```shell
        $ yasboot host add -c yashandb -t hosts_add.toml
        ```

        >  **Note**: 
        >
        > Complete the following configurations on the **new server(s)** as required:
        >
        > - If the database has enabled the [Resource Management](../../../Database Administration/Resource Management/00Resource Management) function before expansion, the [yasboot host cgroup](../../../Tools Guide/yasboot/Introduction to yasboot Command/yasboot host) command needs to be executed on the new server(s) to create the cgroup directory.
        > 
        >```shell
        >$ yasboot host cgroup create -c yashandb --sudo-username root --sudo-password ****** --host-id host004
        >```
        > 
        > - If other servers were configured for auto-start before expansion, the new server(s) must also [configure auto-start](../../Installation and Deployment/Initial Environment after Installation/Configuring Boot Autostart).

    - Scenario 2: Add CN node(s) on existing server(s)

        Execute the [yasboot config node gen](../../../Tools Guide/yasboot/Introduction to yasboot Command/yasboot config) command based on the actual scenario to generate the [yashandb_add.toml](../../../Tools Guide/yasboot/Configuration Files/Database Scaling Configuration File) configuration file.

        ```shell
        $ yasboot config node gen -c yashandb --host-ids host0001,host0002 -g 2 --node 2
        ```

4. On the server where the above - mentioned configuration file was generated, execute the following command to add CN node(s).

    ``` shell
    $ yasboot node add -c yashandb -t yashandb_add.toml
    +-------------------------------------------------------------------------------------------------+
    | type | uuid             | name    | hostid | index    | status  | return_code | progress | cost |
    +-------------------------------------------------------------------------------------------------+
    | task | faac17066f99af00 | NodeAdd | -      | yashandb | SUCCESS | 0           | 100      | 3    |
    +------+------------------+---------+--------+----------+---------+-------------+----------+------+
    task completed, status: SUCCESS
    ```

    A successful task display does not indicate that all scaling tasks have succeeded, as there may still be background tasks completing data synchronization and other operations. You can use the `task list` command to check whether all expansion-related tasks were successful.

    ```shell
    $ yasboot task list -c yashandb --search type=NodeAdd
    ```

5. (Optional) Backup the database.

    It is recommended to [backup](../../../Database Administration/Backup and Recovery/00Backup and Recovery) the database to ensure that there is a recoverable baseline backup set after scaling.

6. If expansion is performed by adding new servers, after completion, the [[host]] content in hosts_add.toml needs to be copied and pasted to the end of hosts.toml to avoid using old host information during the upgrade.

## Removing CN Nodes

1. Log in to the database installation server using the installation user.


2. Execute the following command to check the target node IDs to be deleted.

    ```shell
    $ yasboot cluster status -c yashandb -d
    # The part before the colon in nodeid is the node ID; for example, for 1-1:1, the node ID is 1-1.
    ```

3. Execute the following command to delete the CN nodes.

    ```shell
    # You can add the --with-host parameter to continue deleting all empty servers in the current cluster with no database nodes and no yasom processes after successfully deleting the nodes.
    $ yasboot node remove -c yashandb --node-ids 2-4 --purge      
    ```

4. (Optional) Backup the database.
    
    It is recommended to [backup](../../../Database Administration/Backup and Recovery/00Backup and Recovery) the database to ensure that there is a recoverable baseline backup set after scaling down.

5. If the --with-host parameter was specified during scaling down (i.e., removing empty servers), after completion, the [[host]] content in hosts.toml needs to be updated to remove the relevant information of the corresponding servers to avoid using old host information during the upgrade.

YashanDB supports online scale out/scale in standby clusters in YAC Deployment through [*yasboot*](../../../Tools Guide/yasboot/00yasboot), and the scaling does not affect the usage of the entire cluster.

> **Note**: 
> 
> - Before performing scaling operations, please carefully read the [Considerations for Scaling](../Considerations for Scaling).
>
> - If scaling operations fail, please refer to [Exception Handling for Scaling](../Exception Handling for Scaling) for resolution.

## Scaling Out Standby Cluster Online

Each instance of the new standby cluster is hosted on a single separate server (referred to as the "new server" hereinafter). It is recommended that the operating system version of the new server be consistent with that of the existing servers.

1. Prepare the new servers and storage devices required for scaling out, and refer to [Pre-installation Preparation](../../../Installation and Upgrade/Installation and Deployment/Pre-Installation Preparation/00Pre-Installation Preparation) to check and ensure that all environmental requirements of the new server system have been met.

    The number of instances in the new standby cluster must not exceed the maximum value of THREAD# in the GV$LOGFILE view (you can execute `SELECT MAX(thread#) AS max_thread# FROM V$LOGFILE;` to check and confirm).

2. Log in to the server where an existing node of the database is located as the installation user.

3. Complete the following preparatory work according to the deployment scale before this scaling out:

    ::: tabs
    == Current is single cluster deployment

1. ) You need to correctly configure the primary/standby cluster communication address, which should be the IP address of the current node and an unused port (e.g., 1690).

```shell
# Log in as sys user without password

$ yasql / as sysdba
# Or log in to the database as DBA user

$ yasql sales/********@192.168.1.2:1688

-- View the REPLICATION_ADDR parameter value

SQL> show parameter REPLICATION_ADDR

NAME                                                              VALUE
----------------------------------------------------------------  ----------------------------------------------------------------
REPLICATION_ADDR

-- If the parameter value is empty or incorrect, you need to modify the configuration

SQL> ALTER SYSTEM SET REPLICATION_ADDR=192.168.1.2:1690 scope=spfile;

SQL> exit

# Log in to other instances as sys user or DBA user and repeat the above operations

$ yasql sales/********@192.168.1.3:1688
SQL> show parameter REPLICATION_ADDR

NAME                                                              VALUE                                                            
----------------------------------------------------------------  ---------------------------------------------------------------- 
REPLICATION_ADDR

SQL> ALTER SYSTEM SET REPLICATION_ADDR=192.168.1.3:1690 scope=spfile;

SQL> exit
```

2. ) Check and ensure that archiving mode is enabled.

```sql
SELECT database_name,log_mode,open_mode FROM V$DATABASE;

DATABASE_NAME      LOG_MODE          OPEN_MODE
------------------ ----------------- ------------
yashandb           ARCHIVELOG        READ_WRITE
```

LOG_MODE = ARCHIVELOG indicates that archiving is enabled; you only need to restart to make the REPLICATION_ADDR parameter configuration take effect. Otherwise, please refer to [Enabling Archiving](../../../Database Administration/Instance Management/Archive Management.md#EnableArchiving) (the database needs to be restarted during this process).

    == Current is primary/standby cluster deployment

Check and disable yasom election.

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

    :::

4. Execute the [yasboot config group gen](../../../Tools Guide/yasboot/Introduction to yasboot Command/yasboot config) command to generate the configuration file for adding standby cluster.

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

7. On the server where the configuration file was generated, execute the following command to add standby cluster(s).

    ``` shell    
    $ yasboot group add -c yashandb -t yashandb_add.toml
    ```

8. (Optional) Backup the database.

    It is recommended to perform a [backup](../../../Database Administration/Backup and Recovery/00Backup and Recovery) of the database to ensure there is a baseline backup set available for recovery after scaling.

9. Copy the [[host]] content from hosts_add.toml to the end of hosts.toml to avoid using old host information during an upgrade.

10. You can optionally enable the [yasom election](../../../High Availability/Configuring Leader Election/Configuring yasom Election for One Primary and One Standby) function as needed to ensure business continuity.

## Scaling in Standby Cluster Online

1. Log in to the database installation server using the installation user.


2. Check and turn off the yasom arbitration for master selection:

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

3. Execute the following command to view the ID of the target standby cluster to be deleted.

    ```shell
    $ yasboot cluster status -c yashandb -d
    # The part before the hyphen in nodeid is the cluster group ID; for example, for 2-1:3, the cluster group ID is 2
    ```

4. Execute the following command to delete the standby cluster.

    ``` shell
    # You can add the --with-host parameter to continue deleting information of all empty servers without databases and without yasom processes in the current distributed cluster after successfully deleting the node group.
     $ yasboot group remove --group-ids 2 -f --purge --ce -c yashandb --with-host
    ```

5. (Optional) Backup the database.
    
    It is recommended to perform a [backup](../../../Database Administration/Backup and Recovery/00Backup and Recovery) of the database to ensure that a baseline backup set is available for recovery after scaling in.

6. If the --with-host parameter is specified during scale-in (that is, empty servers are removed), update the [[host]] entries in the hosts.toml file after the scale-in operation completes by deleting the information of the corresponding servers. This prevents the upgrade process from using outdated host configuration.

7. If the standby cluster is deleted and the deployment still remains as a Primary/Standby cluster deployment, you can optionally enable the [yasom election](../../../High Availability/Configuring Leader Election/Configuring yasom Election for One Primary and One Standby) function as needed to ensure business continuity.

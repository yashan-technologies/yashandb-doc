Cascade standby database is a physical standby database that receives logs from a certain physical standby database. YashanDB supports adding a new Cascade standby database to an existing standby database (the upper-level standby database).

> **Note**: 
>
> - Currently, it is not possible to directly reduce the cascade standby database.
>
> - Before performing scaling operations, please carefully read the [Considerations for Scaling](../Considerations for Scaling).
>
> - If any exceptions occur during scaling, such as node failures or scaling failures, please refer to [Exception Handling for Scaling](../Exception Handling for Scaling) for resolution.

## Step 1: Prepare New Standby Database

Each standby database will be hosted by a separate server (hereinafter referred to as "new server"). It is recommended that the new server's OS version be consistent with existing server(s).

1. Prepare the new servers required for scaling out, and refer to [Pre-installation Preparation](../../../Installation and Upgrade/Installation and Deployment/Pre-Installation Preparation/00Pre-Installation Preparation) to check and ensure that all environmental requirements of the new server system have been met.

2. Log in to the server where an existing node of the database is located as the installation user.

3. Check and disable yasom election:

    If it is a one-primary/one-standby environment, disable yasom election before proceeding. If it is a one-primary/multi-standby environment, it does not affect online scaling in whether the leader election is enabled or not.

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
    -u yashan -p password --ip 192.168.1.7 --port 22 \
    --install-path /data/yashan/yasdb_home \
    --data-path /data/yashan/yasdb_data \
    --log-path /data/yashan/log \
    --node 1
    ```
    
    After successful execution, two configuration files will be generated: [yashandb_add.toml](../../../Tools Guide/yasboot/Configuration Files/Database Scale-out Configuration File) and [hosts_add.toml](../../../Tools Guide/yasboot/Configuration Files/Server Scale-out Configuration File). 

5. Execute the following command to remotely connect to the new server(s) and install YashanDB on them.

    ```shell
    $ yasboot host add -c yashandb -t hosts_add.toml
    ```

6. On the server where the configuration file was generated, execute the following command to add standby database(s).

    ``` shell    
    # If the database has enabled key management before scaling out, --wallet-password Your_keystore_password must be specified

    $ yasboot node add -c yashandb -t yashandb_add.toml
    ```

    The task being marked as successful does not mean that all scaling out tasks have been fully completed; some background tasks may still be synchronizing data and performing other operations. You can check whether all tasks related to scaling out are successful by using the `task list` command.

    ```shell
    $ yasboot task list -c yashandb --search type=NodeAdd
    ```

7. If all other servers have been configured for auto-start before the scaling out, Log in to all new servers to be [configured for auto-start](../../Installation and Deployment/Initial Environment after Installation/Configuring Boot Autostart).


## Step 2: Convert New Standby Database to Cascade Standby

The conversion of the new standby database to cascade standby role is controlled by the ARCHIVE_DEST_* parameters, and the VALID_FOR keyword distinguishes the effective range of the replication link:

- VALID_FOR = PRIMARY_ROLE (default): Indicates that this link is only effective when the current database is a primary database; this link can only be from primary database → standby database.

- VALID_FOR = STANDBY_ROLE: Indicates that this link is only effective when the current database is a standby database; this link can only be from standby database → cascade standby.

- VALID_FOR = ALL_ROLES: Indicates that this link is always effective. When the current database is a primary database, this link is from primary database → standby database; when the current database is a standby database, this link is from standby database → cascade standby.

**New Cascade Standby Configuration Example** (effective after restarting the new Cascade standby, must be executed on each new Cascade Standby Database)

```sql
-- Clear the existing ARCHIVE_DEST_* parameter configurations on the current standby database. The {n} in the following statements should be replaced with specific values, where n indicates the existing number of standby databases on the current standby database.
ALTER SYSTEM SET ARCHIVE_DEST_{1}='' scope=both;
……
ALTER SYSTEM SET ARCHIVE_DEST_{n}='' scope=both;

-- Optional Operation
-- The path conversion configuration adjustments among all primary/standby nodes have been automatically completed by *yasboot* during scaling out. As a cascade standby database, it only needs to retain the path conversion to its upper-level standby database 1. If necessary, excess configurations can be manually deleted, but not modifying them will not affect usage.
-- /data/yashan/yasdb_data/db-1-2 is the path of the upper-level standby database 1, /data/yashan/yasdb_data/db-1-{n+1} is the path of the new cascade standby.
ALTER SYSTEM SET DB_FILE_NAME_CONVERT=
'/data/yashan/yasdb_data/db-1-2','/data/yashan/yasdb_data/db-1-{n+1}' scope=both; 
ALTER SYSTEM SET REDO_FILE_NAME_CONVERT=
'/data/yashan/yasdb_data/db-1-2','/data/yashan/yasdb_data/db-1-{n+1}' scope=both; 
ALTER SYSTEM SET DB_BUCKET_NAME_CONVERT=
'/data/yashan/yasdb_data/db-1-2','/data/yashan/yasdb_data/db-1-{n+1}' scope=both; 
```

**Upper-Level Standby Database 1 Configuration Example**

```sql
-- The {n+1}~{n+m} in the following statements should be replaced with specific values, where n indicates the existing number of cascade standby databases on upper-level standby database 1. For each new cascade standby database added, the corresponding number of ARCHIVE_DEST_* parameters needs to be configured.
ALTER SYSTEM SET ARCHIVE_DEST_{n+1}='SERVICE=192.168.1.7:1689 VALID_FOR=ALL_ROLES' scope=both;
……
ALTER SYSTEM SET ARCHIVE_DEST_{n+m}='SERVICE=New Cascade Standby's REPLICATION_ADDR VALID_FOR=ALL_ROLES' scope=both;

-- The path conversion configuration adjustments have been automatically completed by *yasboot* during scaling out, and no manual handling is needed here.
```

**Configuration Example for Other Primary-Standby Databases in the Environment**

```sql
-- Optional Operation
-- The path conversion configuration adjustments among all primary/standby nodes have been automatically completed by *yasboot* during scaling out. The path conversion information configured on other master standby databases unrelated to the new cascade standby databases is meaningless, and if necessary, excess configurations can be manually deleted; not modifying them will not affect usage.
ALTER SYSTEM SET DB_BUCKET_NAME_CONVERT=
'/data/yashan/yasdb_data/db-1-2','/data/yashan/yasdb_data/db-1-{n+1}' scope=both; 
```

## Step 3: Post-Expansion Operations

1. (Optional) Backup the database.

    It is recommended to perform a [backup](../../../Database Administration/Backup and Recovery/00Backup and Recovery) of the database to ensure that there is a baseline backup set available for recovery after the scaling out.

2. Copy the [[host]] content from hosts_add.toml and paste it at the end of hosts.toml to avoid using old hosts information during upgrades.

3. If any configurations have been temporarily adjusted for successful scaling out (e.g., disabling yasom election), restore the configuration as needed after scaling out is complete (e.g., re-enable [yasom election](../../../High Availability/Configuring Leader Election/Configuring yasom Election)).

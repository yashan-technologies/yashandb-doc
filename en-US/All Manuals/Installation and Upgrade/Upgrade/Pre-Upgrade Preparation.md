## Obtain New Version Installation Package

Please confirm with YashanDB technical support whether the currently used database version can be directly upgraded to the target new version, and obtain the installation package for the new version.

## Rolling Upgrade Environment Validation

To perform a [rolling upgrade](./升级操作/滚动升级), ensure the current database meets the following conditions:

- Database operates in yashan mode.
  
    Verify the database mode using the following SQL statement:
    
    ```sql
    show parameter compat_vector
  
    name                                                              value                                                            
    ----------------------------------------------------------------  ---------------------------------------------------------------- 
    COMPAT_VECTOR                                                     yashan                                                           
    ```

    If the current environment operates in mysql mode, use [offline upgrade](./升级操作/离线升级) instead.

- Database contains no identity columns.

    Check for identity columns in the database using the following SQL statement:

    ```sql
    SELECT COUNT(*) FROM DBA_TAB_COLS WHERE IDENTITY_COLUMN = 'Y';
    
                count(*) 
    --------------------- 
                        0
    ```

    If identity columns exist in the current database, use [offline upgrade](./升级操作/离线升级) instead.

## Check Dependencies

Please refer to [Dependency Preparation](../安装部署/安装前准备/依赖项准备) to check and ensure that the dependencies for all servers meet the requirements.

## Check SSH Service

Upgrade requires SSH and SFTP services. Please check whether the SSH service on each server is enabled and whether the operational user (it is recommended to use the installation user directly) is allowed to log in remotely.

- You can check whether the SSH service is enabled with the following command:

  ```shell
  $ systemctl status sshd.service
  ● sshd.service - OpenSSH server daemon
     Loaded: loaded (/usr/lib/systemd/system/sshd.service; enabled; vendor preset: enabled)
     Active: active (running) since Wed 2023-04-26 14:32:52 CST; 3 months 6 days ago
       Docs: man:sshd(8)
             man:sshd_config(5)
   Main PID: 6372 (sshd)
     CGroup: /system.slice/sshd.service
             └─6372 /usr/sbin/sshd -D
  ```

- You can check whether the operational user can log in remotely during the upgrade with the following command:

  Use the current user to SSH into the current server. SSH can use a password or keyless login:

  ```shell
  $ ssh username@IP -p SSH port
  ```

  Enter the password as prompted by the command and check if you can successfully connect to the SSH server.

## Upload New Version Installation Package

Please replace the path, installation package name, etc., with actual values.

1. Log in to the database installation server using the installation user.


2. Create an empty directory, which will serve as a temporary storage path for the upgrade installation package.

    ```shell
    $ cd /home/yashan
    $ mkdir tmp_upgrade20250930
    ```
    > **Note**:
    >
    > `tmp_upgrade20250930` is only an example value. It is recommended to use the new version number or operation date to name this directory during actual operations, facilitating management and differentiation.
    
3. Upload the new installation package to /home/yashan/tmp_upgrade.

4. Enter the directory where the upgrade installation package is stored and extract the installation package.

    ```shell
    $ cd tmp_upgrade
    $ tar zxf yashandb-{new version number}-linux-x86_64.tar.gz
    ```

<span id="sys-data" name="sys-data" class="yaslink"></span>

## Plan System Disk (YAC Deployment)

In YAC Deployment, if you need to perform an offline upgrade from version 23.2.x.x or earlier to version 23.4.x.x or later, this operation must be performed.

1. Plan the system disk on shared storage. For specific operations, please refer to [Disk Partitioning](../安装部署/安装前准备/配置存储设备.html#DiskPartitioning).

2. Create a disk_config.toml file in the tmp_upgrade directory. Refer to [YAC Upgrade Configuration File](../../工具手册/yasboot/配置文件/共享集群升级配置文件) and edit the file content based on the actual disk planning. Confirm correctness before saving and exiting.

    ```shell
    $ vi disk_config.toml
    
    [[group]]
        group_id = 1                    # YAC group ID
        disk_found_path = "/dev/yfs"    # Specify the disk discovery path, which is the directory above the data disk. Disk information can be queried with the yfscmd show disk command.
        system_data = ["/dev/yfs/sys0", "/dev/yfs/sys1", "/dev/yfs/sys2"]   # Specify the planned system disk paths.
    
    [[group]]                           # For primary/standby cluster deployments, modify the standby cluster based on the example of the primary cluster. If not needed, you can delete this.
        group_id = 2        
        disk_found_path = "/dev/yfs2"
        system_data = ["/dev/yfs2/sys0", "/dev/yfs2/sys1", "/dev/yfs2/sys2"]
    ```

## Check hosts.toml File

The default path for the hosts.toml file is /home/yashan/install/hosts.toml.

### Check *yasom* Information

If you have performed any relevant switching operations for yasom before the upgrade, please ensure that the om's LISTEN_ADDR in the hosts.toml is the current primary yasom.

1. Query the yasom related information in the current environment.

    ```shell
    $ yasboot process yasom status -c yashandb
    +------------------------------------------------------------------------------------------------------------------------------------+
    | hostid   | pid   | ipaddr       | primary           | secondary | local_yasom_addr  | role    | backup_num | max_seq | auto_repair |
    +------------------------------------------------------------------------------------------------------------------------------------+
    | host0001 | -     | 192.168.1.2  | 192.168.1.3:1675  | []        | -                 | -       | 2          | 54      | -           |
    +----------+-------+--------------+-------------------+-----------+-------------------+---------+------------+---------+-------------+
    | host0002 | 27584 | 192.168.1.3  | 192.168.1.3:1675  | []        | 192.168.1.3:1675  | primary | 2          | 54      | off         |
    +----------+-------+--------------+-------------------+-----------+-------------------+---------+------------+---------+-------------+
    ```

    According to the echo output, the LISTEN_ADDR of yasom in the current environment is `192.168.1.3:1675`, and the hostid of the server it resides on is `host0002`.  

2. Confirm whether the local_yasom_addr of the yasom with role primary is the same as the om's `LISTEN_ADDR` and `hostid` in the hosts.toml. If they are different, manually modify the om's LISTEN_ADDR and hostid in the hosts.toml to match the primary yasom.

    ```toml
    $ cat hosts.toml
    
    ……
    [om]
      hostid = "host0001"	
      [om.config]	
        LISTEN_ADDR = "192.168.1.2:1675"
    ……
    # The LISTEN_ADDR and hostid of yasom in the hosts.toml file are inconsistent with the actual values and need to be modified  
    
    $ vi hosts.toml
    
    ……
    [om]
      hostid = "host0002"	
      [om.config]	
        LISTEN_ADDR = "192.168.1.3:1675"
    ……
    ```

### Check server information

If you have performed an expansion operation for newly added servers before the upgrade, please ensure that the information of the newly added servers has been appended to the hosts.toml file.

## Confirm Privilege and Space of Temporary Directory

###  Rolling Upgrade

During [Rolling Upgrade](./升级操作/滚动升级), a directory for installing the new version of the database will be created. Please ensure the operational user has the privilege to create this directory and has sufficient storage space.

<span id="space" name="space" class="yaslink"></span>

###  Offline Upgrade

[Offline upgrade](./升级操作/离线升级) will automatically perform database backup. The backup scope includes control files, redo files, SYSTEM tablespace files, SYSAUX tablespace files, and UNDO tablespace files of the current database. The backup directory can be custom planned as needed (manually created before upgrade), or the default directory (automatically created during upgrade) can be used.

If the upgrade fails, this backup set will be used when executing offline upgrade rollback.

####  Capacity Estimation

The capacity of the backup path must be higher than the sum of the backup essential capacity and additional reserved capacity. The system will first check whether the capacity meets the requirements and then determine whether to continue executing subsequent operations based on the check results.

| Capacity Classification | Capacity Estimation (Query) Methods            |
|-------------|-------------------------|
| Backup essential capacity | **Method 1**: Query and sum file sizes via SQL<br/>`SELECT (SELECT SUM(BYTES) FROM V$CONTROLFILE) + (SELECT SUM(USED_BLOCKS * BLOCK_SIZE) FROM V$LOGFILE) + (SELECT SUM(BYTES) FROM DBA_DATA_FILES WHERE TABLESPACE_NAME IN ('SYSTEM', 'SYSAUX', 'UNDO')) AS total_capacity FROM DUAL;`<br/><br/>**Method 2**: Estimate based on total capacity of `$YASDB_DATA/dbfiles` path on each server|
| Additional reserved capacity          | The default value is 5G, which can be customized through the --backup-reserved-space parameter      |

####  Custom Backup Path and Permission Requirements

If a custom backup path is needed, a path with sufficient capacity must be created and appropriate permissions configured before upgrade. During upgrade, this path must be specified as the backup path through the --backup-path parameter.

| Deployment Form | Backup Location | Permission Requirements |
|--------------------|-------------|-------------------------|
| Standalone Deployment<br/>ISC Distributed Cluster Deployment | Create corresponding paths on each server, and the paths on each server should be consistent | The installation user has read and write permissions for corresponding paths |
| YAC Deployment | Create corresponding paths on the server hosting the master instance | The installation user has read and write permissions for this path |

After successful upgrade or successful rollback, this directory will be automatically cleared.

####  Default Backup Path and Permission Requirements

If the default backup path is to be used, there is no need to manually create the path, but it must be ensured that the upgrade operation user has permission to create the path, and the parent directory ${old_version_YASDB_HOME} where the default backup path is located has sufficient capacity.

| Deployment Form | Backup Location |
|--------------------|-------------|
| Standalone Deployment<br/>ISC Distributed Cluster Deployment | ${Old_version_YASDB_HOME}/upgrade_tmp/backup on each server |
| YAC Deployment | ${Old_version_YASDB_HOME}/upgrade_tmp/backup on the server hosting the master instance |

After successful upgrade or successful rollback, this directory will be automatically deleted.

## Temporarily Terminate Daemons

Before upgrading, please check and terminate the daemons on the servers where the instances reside. If auto-start at boot has been configured, you need to temporarily [disable auto-start](../Installation and Deployment/Initial Environment after Installation/Configuring Boot Autostart.html#disable).

> **Caution**:
>
> This step only requires terminating daemons related to YashanDB; there is no need to terminate other application-related or system monit processes.

```shell
# Execute yasboot monit status command or yasboot monit summary command to confirm if there are any monit processes
$ yasboot monit status -c yashandb

# If they exist, terminate the YashanDB related daemons
$ yasboot monit stop -c yashandb
+---------------------------------------------------------------------------------------------------------+
| type | uuid             | name            | hostid | index    | status  | return_code | progress | cost |
+---------------------------------------------------------------------------------------------------------+
| task | eac7dd31c8a589e3 | MonitParentStop | -      | yashandb | SUCCESS | 0           | 100      | 1    |
+------+------------------+-----------------+--------+----------+---------+-------------+----------+------+
task completed, status: SUCCESS
```

## Temporarily Disable *yasom* Election

For one-primary/one-standby standalone deployments or distributed high-availability deployments (where nodes within the DN group are one-primary/one-standby), it is necessary to perform the current operation to check and disable yasom election before proceeding with subsequent operations.

1. Execute the following command to check whether yasom election is enabled:

    ```shell
    $ yasboot election config show -c yashandb
    group 1
      Protection Mode: MAXIMUM PROTECTION
      Members:
        [1-1:1] - Primary database
          [1-2:2] - Physical standby database
                      Transport Lag: 0 seconds
                      Apply Lag:     0 seconds
                      Apply Rate:    3.79 MByte/s

      Properties:
        FailoverThreshold      = 9
        FailoverAutoReinstate  = false
        ZeroDataLossMode       = true

    Automatic Failover: Enabled in Zero Data Loss Mode # Enabled indicates that yasom election is enabled
    ```

2. If it is enabled, execute the following command to disable yasom election:

    ```shell
    $ yasboot election enable off -c yashandb
    ```

After the upgrade is complete, please restore the relevant configurations as needed. For specific operations, please refer to [one-primary/one-standby yasom election](../../高可用/自动选主配置/一主一备yasom仲裁选主).

## Check Database Instance Status

Before upgrading, please ensure that the database instances (including all database instances in primary/standby environments) are in OPEN status. You can check the status of the database instance with the following methods:

::: tabs
== *yasboot* command

Query the database status information through the [yasboot cluster status](../../工具手册/yasboot/yasboot命令介绍/yasboot cluster) command, and confirm the instance status based on the value of the instance_status field.

The `instance_status` field being `open` indicates that the corresponding instance is in OPEN state, `mounted` indicates that the instance is in MOUNT stage, and `started` indicates that the instance is in NOMOUNT stage,

```shell
$ yasboot cluster status -c yashandb -d
+-----------------------------------------------------------------------------------------------------------------------------------------------+
| hostid   | node_type | nodeid | pid   | instance_status | database_status | database_role | listen_address   | data_path                      |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+------------------+--------------------------------+
| host0001 | db        | 1-1:1  | 54814 | open            | normal          | primary       | 192.168.1.2:1688 | /data/yashan/yasdb_data/db-1-1 |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+------------------+--------------------------------+
| host0002 | db        | 1-2:2  | 49529 | open            | normal          | standby       | 192.168.1.3:1688 | /data/yashan/yasdb_data/db-1-2 |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+------------------+--------------------------------+
| host0003 | db        | 1-3:3  | 49582 | open            | normal          | standby       | 192.168.1.4:1688 | /data/yashan/yasdb_data/db-1-3 |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+------------------+--------------------------------+           
```

== SQL statement

Query the status of corresponding instances through the [GV$INSTANCE](../../参考手册/系统视图/动态视图/GV$INSTANCE) or [V$INSTANCE](../../参考手册/系统视图/动态视图/V$INSTANCE) views.

In Standalone Deployment, V$INSTANCE needs to be queried by logging into each instance. In other deployment forms, GV$INSTANCE can be queried by logging into any instance.

```sql
-- V$INSTANCE
SELECT STATUS FROM V$INSTANCE;

STATUS            
----------------- 
OPEN 

-- GV$INSTANCE
SELECT GROUP_ID,GROUP_NODE_ID,INST_ID,STATUS FROM GV$INSTANCE;
```
:::

## Check YFS DiskGroup Status (YAC Deployment)

In YAC Deployment, before upgrading, please ensure that all YFS DiskGroups are in a normal mounted state. You can check the status of the DiskGroups in the following methods:

::: tabs
== *yfscmd* command

Query the detailed information of the DiskGroup through the [show diskgroup](../../工具手册/yfscmd/状态查看命令) command, and confirm its status based on the value of the `stat` field.

The `stat` field being `MOUNTED` indicates that the corresponding DiskGroup is in mounted state, while `DISMOUNTED` indicates that it is not currently mounted.

```shell
$ yfscmd -D $YASCS_HOME
YFSCMD > show diskgroup
```

== SQL statement

Query the status of corresponding instances through the [GV$YFS_DISKGROUP](../../参考手册/系统视图/动态视图/GV$YFS_DISKGROUP) views.

```sql
SELECT INST_ID,NAME,STATE FROM GV$INSTANCE;
```

:::

## Full Checkpoint

Wait for the primary database redo to be fully synchronized to the standby database, then execute a full checkpoint on all primary and standby databases to speed up the upgrade process.

```sql
ALTER SYSTEM CHECKPOINT;
```

## Full Backup

YashanDB provides two backup methods: via SQL statement and using the *yasrman* tool. For more information about backup syntax and instructions, please refer to [Backup and Recovery](../../数据库管理/备份与恢复/00备份与恢复).

- Using SQL Statement: Suitable for Standalone/YAC/Distributed Cluster Deployment

  The SQL statement for backup needs to log in to the database instance to operate. You can perform a full backup by executing the following statement:

  ```sql
  BACKUP DATABASE FULL FORMAT '/home/yashan/full_20221123' TAG 'full' PARALLELISM 8 COMPRESSION ALGORITHM ZSTD LOW;
  ```

- Using *yasrman* Tool: Suitable for all deployment types

  To perform a full backup using the *yasrman* tool, ensure to operate on the server where the *yasrman* tool is located. You can perform a full backup by executing the following command:

  ```shell
  # Standalone Deployment, YAC Deployment 
  $ yasrman sys/********@192.168.1.2:1688 -c "BACKUP DATABASE TAG 'full_backup' FULL FORMAT 'full_001' PARALLELISM 8 COMPRESSION ALGORITHM ZSTD LOW" -D /home/yashan/catalog
  
  # ISC Distributed Cluster Deployment 
  $ yasrman sys/********@192.168.1.2:1688 -c "BACKUP CLUSTER TAG 'full_1' FORMAT 'full_bak_1' PARALLELISM 8 COMPRESSION ALGORITHM ZSTD LOW" -D /home/yashan/catalog
  ```

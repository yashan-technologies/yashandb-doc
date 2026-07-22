Control files are one of the most critical physical files in YashanDB, acting as the entry point for the database instance to mount. They record the physical structure of the database (such as the database name, locations and names of data files and redo log files, and timestamps), as well as state information (such as the current log sequence number, checkpoint SCN, archived log information, and RMAN backup details). They serve as essential core information for starting, running, and recovering a database instance. During database startup, control files must be read to locate other files and verify integrity, and they are continuously updated during operation to reflect changes in the database structure and current state. Control files are fundamental to maintaining database consistency and enabling fault recovery.

In YashanDB, the number of control files and their storage paths are independently configured for each instance node.

|Items               |Default Value    |
|--------------------|--------------------------------------|
| Number of Control Files | 3 <br/>Which are copies of each other, with a maximum of 8 supported |
| Name of Control File | Standalone Deployment or ISC Distributed Cluster Deployment: ctrl1、ctrl2、ctrl3<br/>YAC/Distributed Cluster Deployment: ctrlfile0、ctrlfile1、ctrlfile2 |
| Path of Control File | Standalone Deployment or ISC Distributed Cluster Deployment: $YASDB_DATA/dbfiles<br/>YAC/Distributed Cluster Deployment: +DG0/dbfiles |

In actual use, the number of control files and their storage paths should be customized based on factors such as actual disk space planning, disaster recovery requirements, and IO performance optimization strategies. Configuration methods are as follows:

- When installing the database

  - ​​(Recommended)​​ The count of control files and their storage paths can be specified through the configuration parameter [CONTROL_FILES](../../../Reference Manual/Configuration Parameters.md#CONTROL_FILES) in {cluster_name}.toml.

  - For Standalone Deployment or ISC Distributed Cluster Deployment, the count of control files can be specified through the database creation parameter [CTRL_FILE_NUM](../../../Tools Guide/yasboot/Database Creation Parameters) in {cluster_name}.toml. When only this parameter is set, all control files default to path $YASDB_DATA/dbfiles​​.

- Post-installation
  
  - For existing databases, the count of control files and the storage paths for each file can be modified through the configuration parameter [CONTROL_FILES](../../../Reference Manual/Configuration Parameters.md#CONTROL_FILES).

  - When creating a new database, the count of control files and the storage paths for each file can be specified by using the CONTROLFILES parameter in the [CREATE DATABASE](../../../Development Guide/SQL Reference Manual/SQL Statements/CREATE DATABASE) statement.



The information in the control file is maintained automatically by the database, and manual operation of the control file is not permitted, as it could result in unexpected exceptions in the database.



This chapter mainly introduces how to view the control file configuration, adjust the paths of existing control files, add/remove file copies, and handle common exceptions after installation.

## Viewing Control Files

### Viewing through Configuration Parameter

The control file information can be viewed through the CONTROL_FILES parameter, and it is applicable when the database is in any of the NOMOUNT, MOUNT, or OPEN stages.

::: tabs
== View using yasboot

Log in to the database server as the YashanBD installation user and execute the following command to query the relevant information.

```shell
# Scenario 1: View the global control file configuration
$ yasboot cluster config show -c yashandb -q CONTROL_FILES

# Scenario 2: View the control file configuration of a specific node (this article uses node 1-1 as an example)
$ yasboot node config show -c yashandb --node-id 1-1 -q CONTROL_FILES
```

== View using SQL statement

When using SQL queries, only information for the current node can be viewed.

```shell
# 1. Log in to the database 
$ yasql sys/********@192.168.1.2:1688

#2. View the CONTROL_FILES parameter of the current node  
SQL> show parameter CONTROL_FILES;
```
:::

### Viewing through Views

The global or current node's control file information can be viewed through the GV$CONTROLFILE/V$CONTROLFILE views, which require the database to be in the MOUNT or OPEN stage.  

::: tabs
== Standalone Deployment

In Standalone Deployment, the dynamic views can still only query relevant information on the current node.

```sql
SELECT ID,NAME,BLOCK_SIZE,FILE_SIZE_BLKS,BYTES FROM v$controlfile;

  ID NAME                                                BLOCK_SIZE FILE_SIZE_BLKS       BYTES
---- ------------------------------------------------- ------------ -------------- -----------
   0 /data/yashan/yasdb_data/db-1-1/dbfiles/ctrl1            8192           3507    28729344
   1 /data/yashan/yasdb_data/db-1-1/dbfiles/ctrl2            8192           3507    28729344
   2 /data/yashan/yasdb_data/db-1-1/dbfiles/ctrl3            8192           3507    28729344
```

== YAC/Distributed Cluster Deployment

```sql
SELECT INST_ID,ID,NAME,BLOCK_SIZE,FILE_SIZE_BLKS,BYTES FROM gv$controlfile;

    INST_ID           ID NAME                                                               BLOCK_SIZE FILE_SIZE_BLKS                 BYTES
----------- ------------ ---------------------------------------------------------------- ------------ -------------- ---------------------
          1            0 +DG0/dbfiles/ctrlfile0                                                   8192           3097              25370624
          1            1 +DG0/dbfiles/ctrlfile1                                                   8192           3097              25370624
          1            2 +DG0/dbfiles/ctrlfile2                                                   8192           3097              25370624
          2            0 +DG0/dbfiles/ctrlfile0                                                   8192           3097              25370624
          2            1 +DG0/dbfiles/ctrlfile1                                                   8192           3097              25370624
          2            2 +DG0/dbfiles/ctrlfile2                                                   8192           3097              25370624
```

== ISC Distributed Cluster Deployment

```sql
SELECT GROUP_ID,GROUP_NODE_ID,ID,NAME,BLOCK_SIZE,FILE_SIZE_BLKS,BYTES FROM gv$controlfile;

   GROUP_ID GROUP_NODE_ID           ID NAME                                                               BLOCK_SIZE FILE_SIZE_BLKS                 BYTES
----------- ------------- ------------ -------------------------------------------------------------- ------------ -------------- ---------------------
          2             1            0 /data/yashan/yasdb_data/cn-2-1/dbfiles/ctrl1                           8192            479               3923968
          2             1            1 /data/yashan/yasdb_data/cn-2-1/dbfiles/ctrl2                           8192            479               3923968
          2             1            2 /data/yashan/yasdb_data/cn-2-1/dbfiles/ctrl3                           8192            479               3923968
          2             2            0 /data/yashan/yasdb_data/cn-2-2/dbfiles/ctrl1                           8192            479               3923968
          2             2            1 /data/yashan/yasdb_data/cn-2-2/dbfiles/ctrl2                           8192            479               3923968
          2             2            2 /data/yashan/yasdb_data/cn-2-2/dbfiles/ctrl3                           8192            479               3923968
          3             1            0 /data/yashan/yasdb_data/dn-3-1/dbfiles/ctrl1                           8192           3097              25370624
          3             1            1 /data/yashan/yasdb_data/dn-3-1/dbfiles/ctrl2                           8192           3097              25370624
          3             1            2 /data/yashan/yasdb_data/dn-3-1/dbfiles/ctrl3                           8192           3097              25370624
          3             2            0 /data/yashan/yasdb_data/dn-3-2/dbfiles/ctrl1                           8192           3097              25370624
          3             2            1 /data/yashan/yasdb_data/dn-3-2/dbfiles/ctrl2                           8192           3097              25370624
          3             2            2 /data/yashan/yasdb_data/dn-3-2/dbfiles/ctrl3                           8192           3097              25370624
          1             1            0 /data/yashan/yasdb_data/mn-1-1/dbfiles/ctrl1                           8192            479               3923968
          1             1            1 /data/yashan/yasdb_data/mn-1-1/dbfiles/ctrl2                           8192            479               3923968
          1             1            2 /data/yashan/yasdb_data/mn-1-1/dbfiles/ctrl3                           8192            479               3923968
          1             2            0 /data/yashan/yasdb_data/mn-1-2/dbfiles/ctrl1                           8192            479               3923968
          1             2            1 /data/yashan/yasdb_data/mn-1-2/dbfiles/ctrl2                           8192            479               3923968
          1             2            2 /data/yashan/yasdb_data/mn-1-2/dbfiles/ctrl3                           8192            479               3923968
          1             3            0 /data/yashan/yasdb_data/mn-1-3/dbfiles/ctrl1                           8192            479               3923968
          1             3            1 /data/yashan/yasdb_data/mn-1-3/dbfiles/ctrl2                           8192            479               3923968
          1             3            2 /data/yashan/yasdb_data/mn-1-3/dbfiles/ctrl3                           8192            479               3923968
```
:::


## Modifying the Paths of Existing Control Files

The path of the existing control files can be adjusted as needed due to the following considerations:

- Insufficient disk space at the control file location.

- To avoid I/O bottlenecks, distribute the control files across different physical disks.

- To improve system disaster recovery capabilities, place control files on different physical disks.

> **Warn**:
>
> Do not move control files solely at the OS level.

### Preparation

- Modifying existing control file paths requires restarting the target database node whose configuration is being modified (hereinafter referred to as the 'target database node').

- If you need to move an existing control file to another path, you must **strictly follow** the following operation sequence:

  1. Modify the control file path configuration (CONTROL_FILES parameter).

  2. Shut down the target database node.

  3. Move the corresponding physical file to the target path.

  4. Start the target database node.

- Prepare new storage paths for the target control files (multiple file paths can be adjusted in a single operation) on the server hosting the target database node. The YashanDB installation user (i.e., yashan) must have full privileges for the corresponding paths.

### Operation Steps

This chapter takes adjusting the path of the existing control file ctrl3 in a running test environment as an example. In actual operations, replace the path, user, and other sample values with actual values.



1. Log in to the database installation server using the installation user.


2. Obtain information related to the target node.  

    ```shell
    # View the target node ID 
    $ yasboot cluster status -c yashandb -d

    # View the control file configuration of the target node (this chapter uses node 1-1 as an example)
    $ yasboot node config show -c yashandb --node-id 1-1 -q control_files
    +---------------------------------------------------------------------------------------------------------------------------------+
    | parameters    | area                                     | default_value                  | current_value                       |
    +---------------------------------------------------------------------------------------------------------------------------------+
    | CONTROL_FILES | (control file 1, control file 2, …control file n)      | ('?/dbfiles/ctrl1', '?/dbfiles | ('?/dbfiles/ctrl1', '?/dbfiles/ctrl |
    |               |                                          | /ctrl2', '?/dbfiles/ctrl3')    | 2', '?/dbfiles/ctrl3')              |
    +---------------+------------------------------------------+--------------------------------+-------------------------------------+
    ```



3. Modify the control file configuration parameter on the target database node.

    ::: tabs
    == Modify using yasboot

```shell
# Modify the control file configuration parameter to change the path of ctrl3 to the newly planned target path
$ yasboot node config set -c yashandb --node-id 1-1 -k control_files -v ('/data/yashan/yasdb_data/db-1-1/dbfiles/ctrl1','/data/yashan/yasdb_data/db-1-1/dbfiles/ctrl2','/data/yashan/yasdb_data/db-1-1/backup/ctrl3')
```

    == Modify using SQL statement

```shell
# Log in to the target database node
$ yasql sales/********@192.168.1.2:1688

-- Modify the control file configuration parameter to change the path of ctrl3 to the newly planned target path
SQL> ALTER SYSTEM SET control_files=('/data/yashan/yasdb_data/db-1-1/dbfiles/ctrl1','/data/yashan/yasdb_data/db-1-1/dbfiles/ctrl2','/data/yashan/yasdb_data/db-1-1/backup/ctrl3') scope=spfile;

-- Log out
exit
```
    :::

    At this point, no verification will be performed to check whether the new path exists or is accessible.



4. Shut down the target database node.

    ```shell
    $ yasboot node stop -c yashandb -n 1-1
    ```


5. Log in to the server hosting the target database node using the YashanDB installation user.


6. Move the physical file ctrl3 at the OS level.

    ```shell
    $ mv /data/yashan/yasdb_data/db-1-1/dbfiles/ctrl3 /data/yashan/yasdb_data/db-1-1/backup/ctrl3
    ```

7. Check and confirm the path of the control file ctrl3 at the OS level.

    ```shell
    ls -l /data/yashan/yasdb_data/db-1-1/dbfiles/ctrl1
    ls -l /data/yashan/yasdb_data/db-1-1/dbfiles/ctrl2
    ls -l /data/yashan/yasdb_data/db-1-1/backup/ctrl3
    ```



8. Start the target database node.

    ```shell
    $ yasboot node start -c yashandb -n 1-1
    ```

    A successful database node startup confirms the control file path modification. If startup fails with control file-related errors, refer to [Troubleshooting Common Control File Issues](#ctrlissues).



7. Connect and log in to the target database node as a DBA user.

    ```shell
    $ yasql sales/********@192.168.1.2:1688 
    ```

    

8. At the database level, check and confirm the path of the control file ctrl3.

    ```sql
    SELECT ID,NAME,BLOCK_SIZE,FILE_SIZE_BLKS,BYTES FROM v$controlfile;
      ID NAME                                                BLOCK_SIZE FILE_SIZE_BLKS       BYTES
    ---- ------------------------------------------------- ------------ -------------- -----------
      0 /data/yashan/yasdb_data/db-1-1/dbfiles/ctrl1            8192           3507    28729344
      1 /data/yashan/yasdb_data/db-1-1/dbfiles/ctrl2            8192           3507    28729344
      2 /data/yashan/yasdb_data/db-1-1/backup/ctrl3             8192           3507    28729344
    ```

## Adding Control Files

The number of control file copies can be increased as needed due to the following considerations:

- To improve fault tolerance and prevent single points of failure, the number of copies can be increased.

- To meet stricter high-availability requirements, increase the number of copies in critical production environments.

### Preparation

- Adding control file copies requires restarting the target database node whose configuration is being modified (hereinafter referred to as the 'target database node').

- Prepare new storage paths for additional control file copies (multiple copies can be added in a single operation) on the server hosting the target database node. The YashanDB installation user (i.e., yashan) must have full privileges for the corresponding paths.

- Determine filenames for new copies based on the current control file count, using the format ctrlN (where N increments sequentially from the total existing control files typically).

### Operation Steps

This chapter takes adding a control file copy ctrl4 in a running test environment as an example. In actual operations, replace the path, user, and other sample values with actual values.



1. Log in to the database installation server using the installation user.


2. Obtain information related to the target node.  

    ```shell
    # View the target node ID 
    $ yasboot cluster status -c yashandb -d

    # View the control file configuration of the target node (this chapter uses node 1-1 as an example)
    $ yasboot node config show -c yashandb --node-id 1-1 -q control_files
    +---------------------------------------------------------------------------------------------------------------------------------+
    | parameters    | area                                     | default_value                  | current_value                       |
    +---------------------------------------------------------------------------------------------------------------------------------+
    | CONTROL_FILES | (control file 1, control file 2, …control file n)      | ('?/dbfiles/ctrl1', '?/dbfiles | ('?/dbfiles/ctrl1', '?/dbfiles/ctrl |
    |               |                                          | /ctrl2', '?/dbfiles/ctrl3')    | 2', '?/dbfiles/ctrl3')              |
    +---------------+------------------------------------------+--------------------------------+-------------------------------------+
    ```



3. Modify the control file configuration parameter on the target database node.

    ::: tabs
    == Modify using yasboot

```shell
# Modify the control file configuration parameter to change the path of ctrl3 to the newly planned target path
$ yasboot node config set -c yashandb --node-id 1-1 -k control_files -v ('/data/yashan/yasdb_data/db-1-1/dbfiles/ctrl1','/data/yashan/yasdb_data/db-1-1/dbfiles/ctrl2','/data/yashan/yasdb_data/db-1-1/dbfiles/ctrl3','/data/yashan/yasdb_data/db-1-1/backup/ctrl4')
```

    == Modify using SQL statement

```shell
# Log in to the target database node
$ yasql sales/********@192.168.1.2:1688

-- Modify the control file configuration parameter to change the path of ctrl3 to the newly planned target path
SQL> ALTER SYSTEM SET control_files=('/data/yashan/yasdb_data/db-1-1/dbfiles/ctrl1','/data/yashan/yasdb_data/db-1-1/dbfiles/ctrl2','/data/yashan/yasdb_data/db-1-1/dbfiles/ctrl3','/data/yashan/yasdb_data/db-1-1/backup/ctrl4') scope=spfile;

-- Log out
exit
```
    :::

    At this point, no verification will be performed to check whether the new file exists or the path is accessible.



4. Shut down the target database node.

    ```shell
    $ yasboot node stop -c yashandb -n 1-1
    ```


5. Log in to the server hosting the target database node using the YashanDB installation user.


6. At the OS level, generate a new control file (ctrl4) by copying an existing control file to the target path.

    ```shell
    $ cp /data/yashan/yasdb_data/db-1-1/dbfiles/ctrl3 /data/yashan/yasdb_data/db-1-1/backup/ctrl4
    ```

7. Check and confirm the control file ctrl4 at the OS level.

    ```shell
    ls -l /data/yashan/yasdb_data/db-1-1/dbfiles/ctrl3    
    ls -l /data/yashan/yasdb_data/db-1-1/backup/ctrl4
    ```



8. Start the target database node.

    ```shell
    $ yasboot node start -c yashandb -n 1-1
    ```

    A successful database node startup confirms the control file path modification. If startup fails with control file-related errors, refer to [Troubleshooting Common Control File Issues](#ctrlissues).



9. Connect and log in to the target database node as a DBA user.

    ```shell
    $ yasql sales/********@192.168.1.2:1688 
    ```

    

10. At the database level, check and confirm the control file ctrl4.

    ```sql
    SELECT ID,NAME,BLOCK_SIZE,FILE_SIZE_BLKS,BYTES FROM v$controlfile;
      ID NAME                                                BLOCK_SIZE FILE_SIZE_BLKS       BYTES
    ---- ------------------------------------------------- ------------ -------------- -----------
      0 /data/yashan/yasdb_data/db-1-1/dbfiles/ctrl1            8192           3507    28729344
      1 /data/yashan/yasdb_data/db-1-1/dbfiles/ctrl2            8192           3507    28729344
      2 /data/yashan/yasdb_data/db-1-1/dbfiles/ctrl3            8192           3507    28729344
      3 /data/yashan/yasdb_data/db-1-1/backup/ctrl4             8192           3507    28729344
    ```

## Backup and Recovery of Control Files

Modifying the path of the existing control files or adding additional control file copies are ways to ensure high availability of control files. Additionally, backups and recovery can further enhance the high availability of control files.

In YashanDB, backup and recovery of control files are achieved by backing up and recovering the entire database. For specific operations, please refer to [Backup and Recovery](../../Backup and Recovery/00Backup and Recovery).

## Deleting Control Files

The number of control file copies can be reduced as needed (generally not recommended) due to the following considerations:

- Tight disk resources, reduce unnecessary copies.

> **Warn**:
>
> A minimum of one control file must be preserved at all times.

### Preparation

Deleting control file copies requires restarting the target database node whose configuration is being modified (hereinafter referred to as the 'target database node').

### Operation Steps

This chapter takes deleting a control file copy in a running test environment as an example. In actual operations, replace the path, user, and other sample values with actual values.



1. Log in to the database installation server using the installation user.


2. Obtain information related to the target node.  

    ```shell
    # View the target node ID 
    $ yasboot cluster status -c yashandb -d

    # View the control file configuration of the target node (this chapter uses node 1-1 as an example)
    $ yasboot node config show -c yashandb --node-id 1-1 -q control_files
    +---------------------------------------------------------------------------------------------------------------------------------+
    | parameters    | area                                     | default_value                  | current_value                       |
    +---------------------------------------------------------------------------------------------------------------------------------+
    | CONTROL_FILES | (control file 1, control file 2, …control file n)      | ('?/dbfiles/ctrl1', '?/dbfiles | ('?/dbfiles/ctrl1', '?/dbfiles/ctrl |
    |               |                                          | /ctrl2', '?/dbfiles/ctrl3')    | 2', '?/dbfiles/ctrl3')              |
    +---------------+------------------------------------------+--------------------------------+-------------------------------------+
    ```



3. Modify the control file configuration parameter on the target database node.

    ::: tabs
    == Modify using yasboot

```shell
# Modify the control file configuration parameter to delete the control file ctrl3
$ yasboot node config set -c yashandb --node-id 1-1 -k control_files -v ('/data/yashan/yasdb_data/db-1-1/dbfiles/ctrl1','/data/yashan/yasdb_data/db-1-1/dbfiles/ctrl2')
```

    == Modify using SQL statement

```shell
# Log in to the target database node
$ yasql sales/********@192.168.1.2:1688

-- Modify the control file configuration parameter to delete the control file ctrl3
SQL> ALTER SYSTEM SET control_files=('/data/yashan/yasdb_data/db-1-1/dbfiles/ctrl1','/data/yashan/yasdb_data/db-1-1/dbfiles/ctrl2') scope=spfile;

-- Log out
exit
```
    :::

    Delete the physical file ctrl3 as needed.

4. Restart the target database node activates the configuration changes.

    ```shell
    $ yasboot node restart -c yashandb -n 1-1
    ```

5. Connect and log in to the target database node as a DBA user.

    ```shell
    $ yasql sales/********@192.168.1.2:1688 
    ```

    

6. Check and confirm the control files.

    ```sql
    SELECT ID,NAME,BLOCK_SIZE,FILE_SIZE_BLKS,BYTES FROM v$controlfile;
      ID NAME                                                BLOCK_SIZE FILE_SIZE_BLKS       BYTES
    ---- ------------------------------------------------- ------------ -------------- -----------
      0 /data/yashan/yasdb_data/db-1-1/dbfiles/ctrl1            8192           3507    28729344
      1 /data/yashan/yasdb_data/db-1-1/dbfiles/ctrl2            8192           3507    28729344
    ```


<span id="ctrlissues" name="ctrlissues"></span>

## Troubleshooting Common Control File Issues



YashanDB performs control file verification when starting the instance. The following situations will prevent the instance from starting to the MOUNT and subsequent stages:

- **Control file actual path does not match the CONTROL_FILES parameter configured path**

  When starting the database instance, if an error like `failed to open file …… , errno 2, error message "No such file or directory"` occurs, it indicates that the actual path of the control file does not match the path configured in the CONTROL_FILES parameter. The solution is as follows:

  - Method 1: Modify the control file path at the OS level, or generate the corresponding new files at the path indicated in the error message using the existing files.

  - Method 2: Change the CONTROL_FILES parameter after starting the instance to the NOMOUNT stage and then restart the instance.

- **Database version in the control file is inconsistent with the instance version**

  When starting the database instance, if an error like `database version x.x.x incompatible with YashanDB version x.x.x` occurs, it indicates that the database version in the control files is inconsistent with the instance version. Choose whether to perform a version upgrade based on the actual situation.

- **Database ID of control file group members is inconsistent**

  When starting the database instance, if an error like `database id of the control file does not match other control files` occurs, it indicates that the database ID of control file group members is inconsistent. This situation usually occurs when multiple database instances exist on a single server, and the CONTROL_FILES parameter is configured with control files of different databases. Modify it to the correct control file path.

- **The entire set of control files does not match the database**

  When starting the database instance, if an error like `invalid file: ……, the reason: mismatching with the control file` occurs, it indicates that the entire set of control files does not match the database. The solutions are as follows:

  - Method 1: Find the correct control files at the OS level and move them to correct paths.

  - Method 2: Change the CONTROL_FILES parameter after starting the instance to the NOMOUNT stage and then restart the instance.

  - Method 3: Restore the latest available backup set through [recovery](../../Backup and Recovery/00Backup and Recovery).


- **All control files missing or corrupted**

  If the physical files of all control files are missing or corrupted, it can only be resolved by restoring the latest available backup set through [recovery](../../Backup and Recovery/00Backup and Recovery).


During the installation of a YAC or distributed cluster, YFS automatically creates the system DiskGroup (SYSTEM) and the data DiskGroup (default name: DG0). You can plan and configure hardware devices and specify relevant parameter configurations as needed before and during installation. After installation, no additional configuration is required to begin using the system.

>**Caution**:
>
> After database installation, the above two DiskGroups contain critical information about YAC/distributed cluster, including YFS and the database instance. **Do not delete or clear these DiskGroups**, as doing so may cause the database to fail to start or operate normally. 
>
> This chapter only describes the operations and maintenance of system DiskGroups. For operations and maintenance of the data DiskGroup, please refer to [Data DiskGroup Management](./Data DiskGroup Management).

## Overview

The system DiskGroup is the core storage component of YFS. Compared with data DiskGroups, the system DiskGroup has the following characteristics:

- Used to store YFS's own metadata. YFS relies on the system DiskGroup for startup and initialization.

- Supports redundancy protection and can tolerate a certain number of disk failures.

- Enters protection state when failures occur, with some operations restricted.

- Supports repairing faulty disks through online disk or replace disk.

## System DiskGroup High Availability Mechanism

Relying on the redundancy level attribute of the DiskGroup, as long as at least one available data replica remains, the database can continue to operate normally. However, DBAs should still regularly monitor the status of disks. Once a disk failure is detected, it is essential to promptly replace the faulty disk and rebuild redundancy to prevent the failure from spreading and affecting business operations.

### Redundancy

The [redundancy](../../../Product Concepts/YAC Infrastructure/Yashan File System.md#redundancy) level of the system DiskGroup determines the number of data replicas. 

### FailureGroup

[FailureGroups](../../../Product Concepts/YAC Infrastructure/Yashan File System.md#FailureGroup) are used to define disks that may fail simultaneously, working in conjunction with the multi-replica mechanism to achieve data redundancy. 

### Quorum Requirement

To ensure data consistency, YFS instances have quorum requirements for disk/replica access:

- When a YFS instance starts, the data on the system DiskGroup disks visible to it must satisfy the quorum requirement (more than half of the disks are Normal and data is intact) to obtain a deterministic startup result; otherwise, it cannot start.

- During database operation, it is required that all YFS instances must be able to access more than half of the disks in the system DiskGroup. Instances that cannot meet this requirement will exit the cluster on their own.

<span id="Protection" name="Protection"></span>

### Protection State

When the number of system disk groups in a normal status visible to the YFS master instance fails to reach **a majority**, it will enter the PROTECT state:

- When the system DiskGroup redundancy is Normal (i.e., 3 replicas), all replicas being in normal status is considered as meeting the majority requirement.

- When the system DiskGroup redundancy is High (i.e., 5 replicas), at least 4 replicas being in normal status is considered as meeting the majority requirement.

After entering protection state, the system DiskGroup can only read, not write, and the following operations will be prohibited:

|Operation Type |Prohibited Operations |
| ---------- | ---------- |
| YFS Metadata Operations | Operations for creating a data DiskGroup ([CREATE DISKGROUP](../../../Tools Guide/yfscmd/Disk Management Commands.md#CREATEDG) command). <br />Some operations for modifying an existing DiskGroup ([ALTER DISKGROUP](../../../Tools Guide/yfscmd/Disk Management Commands.md#ALTERDG) command): <br />* DISMOUNT<br />* MOUNT<br />* add_disk_clause<br />* drop_disk_clause<br />* rename_disk_clause<br />* offline_disk_clause<br />* resize_disk_clause |
| YCS Operations | Operations related to cluster management, such as creation/deletion of PDB, configuration changes of VIP/SCAN/NETWORK, etc. |

After repairing the offline disks in the system DiskGroup (ONLINE or REPLACE DISK) to meet the above conditions, the system DiskGroup can exit the protection state.

## Viewing DiskGroup Information

You can view YFS disk configuration, usage, and other related information using commands from the YFS management tool [*yfscmd*](../../../Tools Guide/yfscmd/00yfscmd), or via the corresponding dynamic views.

In daily operations and maintenance work, database administrators should regularly monitor disk usage. If any disk status abnormalities are found (e.g., the status field in disk information), timely maintenance operations such as replacement or re-online should be performed.

### Viewing via *yfscmd*

#### Prerequisites



- The YASCS_HOME environment variable is set correctly.

- The YFS service has been started (you can check the YFS status using the `ycsctl status` command to confirm it is online).



#### Operation Steps

1. Log in to the database installation server using the installation user.


2. Execute the *yfscmd* command to view configuration information about the DiskGroup.

    ::: tabs
    == View DiskGroup information

```shell
$ yfscmd -D $YASCS_HOME
YFSCMD > show diskgroup
id name     type     level    au_size  stat     block_size  total_mb    free_mb     usable_file_mb
0  SYSTEM   SYSTEM   1        1.00MB   MOUNTED  4096        3072        2397        799        
1  DG0      USER     0        32.00MB  MOUNTED  4096        20480       16960       16960

YFSCMD > lsdg
id name     type     level    au_size  stat     block_size  total_mb    free_mb     usable_file_mb
0  SYSTEM   SYSTEM   1        1.00MB   MOUNTED  4096        3072        2397        799        
1  DG0      USER     0        32.00MB  MOUNTED  4096        20480       16960       16960
```
System DiskGroup information includes:

- id: The globally unique ID of the DiskGroup, which will be referred to as dgid in FailureGroup or disk information.
- name: The name of the DiskGroup.
- type: The type of the DiskGroup, where SYSTEM indicates the system DiskGroup.
- redundancy: The redundancy level of the DiskGroup. The redundancy level of each DiskGroup is independent.
  - EXTERNAL: Under this configuration, all data is stored as a single copy with no redundancy.
  - NORMAL: Under this configuration, the system DiskGroup provides [2,3] replicas (the exact number depends on the number of FailureGroups).
  - HIGH: Under this configuration, the system DiskGroup provides 5 replicas.
- au_size: The size of the AU, which is the smallest unit for YFS to allocate disk space.
- stat: The state of the DiskGroup.
  - MOUNTED: Indicates that the DiskGroup is mounted.
  - DISMOUNTED: Indicates that the DiskGroup is not mounted.
  - PROTECT: A unique state for the system DiskGroup, indicating the system DiskGroup enters [protection state](#Protection) due to partial disk failures. At this time, YFS runs in degraded mode.
- block_size: The file block size, with the unit being bytes.
- total_mb: The total capacity of the DiskGroup, with the unit being MB.
- free_mb: The available space in the DiskGroup, with the unit being MB.
- usable_file_mb: The available file size in the DiskGroup, i.e., the available space minus the capacity required for multi-replication, with the unit being MB.

    == View FailureGroup information

```shell
$ yfscmd -D $YASCS_HOME
YFSCMD >  show failgroup
id name     dgid
0  SDG0_0   0
1  DG0_0    1
```

FailureGroup information includes:

- id: The globally unique ID of the FailureGroup, which will be referred to as fgid in disk information.
- name: The name of the FailureGroup.
- dgid: The ID of the DiskGroup to which this FailureGroup belongs.


    == View disk information

```shell
$ yfscmd -D $YASCS_HOME
YFSCMD >  show disk
id interid name     status   fgid dgid au_size  au_count total_mb     free_mb      redundancy path                       partners
0  0       SYSTEM_0 NORMAL   0    0    1.00MB   1024     1024         799          NORMAL     /dev/yfs/sys0              1 2 
1  1       SYSTEM_1 NORMAL   1    0    1.00MB   1024     1024         799          NORMAL     /dev/yfs/sys1              2 0 
2  2       SYSTEM_2 NORMAL   2    0    1.00MB   1024     1024         799          NORMAL     /dev/yfs/sys2              0 1 
3  0       DG0_0    NORMAL   3    1    32.00MB  640      20480        16960        EXTERNAL   /dev/yfs/data0

YFSCMD >  lsdsk
id interid name     status   fgid dgid au_size  au_count total_mb     free_mb      redundancy path                       partners
0  0       SYSTEM_0 NORMAL   0    0    1.00MB   1024     1024         799          NORMAL     /dev/yfs/sys0              1 2 
1  1       SYSTEM_1 NORMAL   1    0    1.00MB   1024     1024         799          NORMAL     /dev/yfs/sys1              2 0 
2  2       SYSTEM_2 NORMAL   2    0    1.00MB   1024     1024         799          NORMAL     /dev/yfs/sys2              0 1 
3  0       DG0_0    NORMAL   3    1    32.00MB  640      20480        16960        EXTERNAL   /dev/yfs/data0
```


For the system DiskGroup, the status of each disk visible to each YFS instance may be inconsistent. The cluster will use the information seen by the master YFS instance as the authoritative source.

Disk information includes:

- id: The globally unique ID of the disk.
- interid: The unique ID of the disk within the DiskGroup.
- name: The name of the disk.
- status: The status of the disk, including:
  - NORMAL: The disk is in normal status
  - OFFLINE_SYNC: A temporary status during the disk online process. Disks in this status are writable but not readable. After the online process is completed, the disk changes to the normal status
  - OFFLINE: The disk is offline
- fgid: The ID of the FailureGroup to which the disk belongs.
- dgid: The ID of the DiskGroup to which the disk belongs.
- au_size: The size of the AU.
- au_count: The number of AUs on the disk.
- total_mb: The total capacity of the disk, with the unit being MB.
- free_mb: The currently available capacity of the disk, with the unit being MB.
- redundancy: The redundancy level of the disk's associated DiskGroup, which can be EXTERNAL, NORMAL, or HIGH.
- path: The disk path.
- partners: The list of partner disks for the current disk, with multiple `interid` values separated by spaces.


    :::

3. View disk read/write information.

    ```shell
    $ yfscmd -D $YASCS_HOME
    YFSCMD >  iostat
    id    name     group_number reads          writes         read_bytes           written_bytes        read_time_us   write_time_us  path
    0     SYSTEM_0 0            0              0              0                    0                    0              0              /dev/yfs/sys0
    1     SYSTEM_1 0            0              0              0                    0                    0              0              /dev/yfs/sys1
    2     SYSTEM_2 0            0              0              0                    0                    0              0              /dev/yfs/sys2
    3     DG0_0    1            3001           6              104017920            50766336             1277976        73832          /dev/yfs/data0
    ```

4. View the file directories and files in the specified DiskGroup.

    ```shell
    $ yfscmd -D $YASCS_HOME
    YFSCMD >  ls DG0
    ycr
    voting
    yasfs.ini
    bootdisk
    diskgroupctrl
    ```

### Viewing via Views

The DiskGroup, FailureGroup, and disk information can be queried separately via the dynamic views [GV$YFS_DISKGROUP](../../../Reference Manual/System Views/Dynamic Performance Views/GV$YFS_DISKGROUP)/[V$YFS_DISKGROUP](../../../Reference Manual/System Views/Dynamic Performance Views/V$YFS_DISKGROUP), [GV$YFS_FAILGROUP](../../../Reference Manual/System Views/Dynamic Performance Views/GV$YFS_FAILGROUP)/[V$YFS_FAILGROUP](../../../Reference Manual/System Views/Dynamic Performance Views/V$YFS_FAILGROUP), and [GV$YFS_DISK](../../../Reference Manual/System Views/Dynamic Performance Views/GV$YFS_DISK)/[V$YFS_DISK](../../../Reference Manual/System Views/Dynamic Performance Views/V$YFS_DISK).

#### Prerequisites

The database is in running state.

#### Operation Steps

1. Connect and log in to the database as a DBA user.
    ```shell
    $ yasql sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64
    
    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux
    
    SQL> 
    ```

2. Query the relevant view fields as needed. This document only lists some common scenarios.

    ::: tabs
    == Retrieve DiskGroup configuration

```sql
SELECT ID,NAME,TYPE,AU_SIZE,REDUNDANCY FROM V$YFS_DISKGROUP WHERE NAME = 'SYSTEM';

          ID NAME                              TYPE                                   AU_SIZE REDUNDANCY
------------ --------------------------------- --------------------------------- ------------ -----------------
           0 SYSTEM                            SYSTEM                                 1048576 NORMAL

SELECT NAME,GROUP_NUMBER FROM V$YFS_FAILGROUP WHERE NAME LIKE 'SDG%';
NAME                              GROUP_NUMBER
--------------------------------- ------------
SDG0_0                                       0
```

    == Monitor DiskGroup state and available capacity

```sql
SELECT NAME,TYPE,STATE,FREE_MB,USABLE_FILE_MB FROM V$YFS_DISKGROUP WHERE NAME = 'SYSTEM';

NAME                              TYPE                              STATE                           FREE_MB        USABLE_FILE_MB
--------------------------------- --------------------------------- ----------------- --------------------- ---------------------
SYSTEM                            SYSTEM                            MOUNTED                            9978                  9978
```

    == Retrieve disk information

```sql
SELECT NAME,PATH,GROUP_NUMBER,PARTNERS,FREE_MB,READ_TIME,WRITE_TIME FROM V$YFS_DISK WHERE NAME LIKE 'SYSTEM%';

   GLOBAL_ID NAME             PATH                  GROUP_NUMBER PARTNERS          FREE_MB             READ_TIME          WRITE_TIME
------------ ---------------- --------------------- ------------ ----------------- ------------------- ------------------ -----------------
           0 SYSTEM_0         /dev/yfs/sys0         0            1 2                              9978                  0                 0
           1 SYSTEM_1         /dev/yfs/sys1         0            2 0                              9978                  0                 0
           2 SYSTEM_2         /dev/yfs/sys2         0            0 1                              9978                  3                 0
```

    :::

## System DiskGroup Failure Handling

> **Caution**:
>
> The system DiskGroup does not support dual fault tolerance. When the system DiskGroup is already in protection state, repair operations should be carried out promptly. Otherwise, if another disk failure occurs, it may cause the cluster to exit or some instances to be unable to access the system DiskGroup.

For the system DiskGroup, the status of each disk visible to each YFS instance may be inconsistent. The cluster will use the information seen by the master YFS instance as the authoritative source. Based on this, system DiskGroup failures can be classified as follows:

| Category | Phenomenon | Impact | Repair Solution |
| -------- | --------------------------- | ----------------------- | ---------------------------- |
| Global Failure | The disk status queried on the master YFS instance shows offline (after synchronization, the target disk(s) on all non-master instances will also become offline) | May trigger the system DiskGroup to enter protection state, and YFS will run in degraded mode | 1. If the original disk is offline due to hardware failure, first fix the hardware issue and ensure **the new disk's path and disk name are exactly the same as the old disk**.<br />2. Execute the online (ONLINE) operation on the target disk. |
| Local Failure | The disk status queried only on a non-master YFS instance shows offline | During a local failure, the YFS master instance can access and use the target disk normally, having no impact on the business. <br />If the non-master instance is promoted to the master before the local failure is repaired, the local failure will spread to a global failure. | Local failures usually occur due to issues in the link for accessing the target disk on the server where the non - master instance is located, or other system problems, rather than faults with the disk itself. **Do not** perform repair operations such as directly unplugging/plugging the disk or restarting the storage devices. <br />1. Check and fix link problems, other system problems, etc., and confirm that the disk is reachable from the OS level. <br />2. Restart the target instance (non - master instance). |

<span id="global" name="global"></span>

###  Repairing Global Failures

The specific operation rules for repairing and re-onlining after disk offline or hardware failure are as follows:



- The target DiskGroup to be operated on has been mounted.

- A single operation can only re-online one offline disk.

- Each disk can have at most one pending repair-type operation task at a time, including: ONLINE, REPLACE.

- Each DiskGroup can have up to 64 repair-type operation tasks concurrently (either in progress or queued).

- The online operation performed on the system DiskGroup cannot be resumed from a checkpoint. If the YFS instance undergoes a role change during execution (e.g., the master instance exits and another instance becomes the master), the online operation may be interrupted. It is recommended to query and confirm the status of the target disk. If it is still in offline status, you need to manually execute the corresponding ALTER DISKGROUP command again.



- If the original disk is offline due to hardware failure, you must first fix the hardware issue and ensure that **the new disk's path and disk name are exactly the same as the old disk** before completing the repair through the re-online operation.

#### Prerequisites



- The YASCS_HOME environment variable is set correctly.

- The YFS service has been started (you can check the YFS status using the `ycsctl status` command to confirm it is online).



- If the original disk is offline due to hardware failure, the hardware issue has been fixed and **the new disk's path and disk name are exactly the same as the old disk**.

- The disk to be onlined is in OFFLINE status.

#### Operation Steps

1. Log in to the database installation server using the installation user.


2. Obtain the relevant information about the target disk.

    ```shell
    $ yasql sales/********@192.168.1.2:1688

    SQL> SELECT d.INST_ID,d.NAME,d.PATH,d.GROUP_NUMBER,d.MOUNT_STATUS FROM GV$YFS_DISK d 
    WHERE d.GROUP_NUMBER IN (SELECT ID FROM GV$YFS_DISKGROUP WHERE TYPE = 'SYSTEM')
    ORDER BY d.NAME;
    
    INST_ID      NAME             PATH                  GROUP_NUMBER MOUNT_STATUS
    ------------ ---------------- --------------------- ------------ -----------------
               1 SYSTEM_0         /dev/yfs/sys0                    0 NORMAL
               2 SYSTEM_0         /dev/yfs/sys0                    0 NORMAL
               1 SYSTEM_1         /dev/yfs/sys1                    0 OFFLINE
               2 SYSTEM_1         /dev/yfs/sys1                    0 OFFLINE
               1 SYSTEM_2         /dev/yfs/sys2                    0 NORMAL
               2 SYSTEM_2         /dev/yfs/sys2                    0 NORMAL
    SQL> exit
    ```
    Record the name of the target disk (SYSTEM_1 in this example) for subsequent operations.

3. Execute the [ALTER DISKGROUP](../../../Tools Guide/yfscmd/Disk Management Commands.md#ALTERDG) command to re-online the target disk.

    ```shell
    $ yfscmd -D $YASCS_HOME
    YFSCMD > exec ALTER DISKGROUP SYSTEM ONLINE DISK SYSTEM_1 POWER 10;
    ```

    You can specify the data migration intensity (the value of the `POWER` keyword) for the current plan based on business load. The higher the value, the greater the intensity and the faster the plan completes, but the higher the resource consumption.

4. If multiple disks have global failures simultaneously, repeat the ALTER DISKGROUP command to repair all failures.

5. Execute the following command to view the progress of this repair plan.

    ```shell
    YFSCMD > show job
    ```
    When the `status` field of the target plan changes to `FINISH`, it indicates the plan has been successfully executed.

6. Confirm that the status of all disks has returned to normal.

    ```shell
    YFSCMD > show disk
    ```

###  Repairing Local Failures

#### Prerequisites



- The YASCS_HOME environment variable is set correctly.

- The YFS service has been started (you can check the YFS status using the `ycsctl status` command to confirm it is online).



- The link problems, other system problems, etc. have been investigated and resolved, and it has been confirmed from the operating system level that the target disk is reachable.

- The target instance where the local failure occurs must remain a non-master instance. If the target instance has become master, it needs to be handled as a [global failure](#global).

#### Operation Steps

1. Log in to the database installation server using the installation user.


2. Obtain the relevant information about the target disk.

    ```shell
    $ yasql sales/********@192.168.1.2:1688

    SQL> SELECT d.INST_ID,d.NAME,d.PATH,d.GROUP_NUMBER,d.MOUNT_STATUS FROM GV$YFS_DISK d 
    WHERE d.GROUP_NUMBER IN (SELECT ID FROM GV$YFS_DISKGROUP WHERE TYPE = 'SYSTEM')
    ORDER BY d.NAME;
    
    INST_ID      NAME             PATH                  GROUP_NUMBER MOUNT_STATUS
    ------------ ---------------- --------------------- ------------ -----------------
               1 SYSTEM_0         /dev/yfs/sys0                    0 NORMAL
               2 SYSTEM_0         /dev/yfs/sys0                    0 NORMAL
               1 SYSTEM_1         /dev/yfs/sys1                    0 NORMAL
               2 SYSTEM_1         /dev/yfs/sys1                    0 NORMAL
               1 SYSTEM_2         /dev/yfs/sys2                    0 OFFLINE
               2 SYSTEM_2         /dev/yfs/sys2                    0 NORMAL
    SQL> exit
    ```
    Record the name of the target disk (SYSTEM_2 in this example) for subsequent operations.

3. Obtain the YFS instance role.

    ```shell
    $ ycsctl status
    +--------------+-------------------+-----------------+---------------------------------------------+
    | Self Host ID | Cluster Master ID | YasFS Master ID |                Active Host Count            |
    +--------------+-------------------+-----------------+---------------------------------------------+
    |1             |2                  |2                |2                                            |
    +--------------+-------------------+-----------------+---------------------------------------------+
    ……
    ```
    Self Host ID represents the current node's ID, while YasFS Master ID represents the node ID where the master YFS instance resides. If the current node is not the YFS master instance's node, continue with subsequent operations; otherwise, you need to repair the [global failure](#global).


4. Execute the following command to restart the target instance.

    ```shell
    $ yasboot node restart -c yashandb -n 1-1
    ```

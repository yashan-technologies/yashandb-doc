During the installation of a YAC or distributed cluster, YFS automatically creates the system DiskGroup (SYSTEM) and the data DiskGroup (default name: DG0). You can plan and configure hardware devices and specify relevant parameter configurations as needed before and during installation. After installation, no additional configuration is required to begin using the system.
 
>**Caution**:
>
> Once the system DiskGroup is created, it **cannot** be expanded or have faulty disks replaced, or undergo other configuration changes.
>
> After database installation, the above two DiskGroups contain critical information about YAC/distributed cluster, including YFS and the database instance. **Do not delete or clear these DiskGroups**, as doing so may cause the database to fail to start or operate normally. 

<span id="Viewing" name="Viewing"></span>

## Viewing DiskGroup Information

You can view YFS disk configuration, usage, and other related information using commands from the YFS management tool [*yfscmd*](../../../Tools Guide/yfscmd/00yfscmd), or via the corresponding dynamic views.

In routine operations, DBAs should regularly monitor disk usage. If the data DiskGroup's storage space becomes insufficient (e.g., the `usable_file_mb` field in DiskGroup information), timely expansion is required. If disk status anomalies are detected (e.g., the `status` field in disk information), timely maintenance operations such as disk replacement or re-onlineing are required.

### Viewing via *yfscmd*

#### Prerequisites



- The YASCS_HOME environment variable is set correctly.

- The YFS service has been started (you can check the YFS status using the `ycsctl status` command to confirm it is online).



#### Operation Steps

1. Log in to the database installation server using the installation user.


2. Execute the *yfscmd* command to view configuration information about the DiskGroup.

    ::: tabs
    == View DiskGroup list

```shell
$ yfscmd -D $YASCS_HOME ls
SYSTEM
DG0

$ yfscmd -D $YASCS_HOME
YFSCMD > ls
SYSTEM
DG0
```
- SYSTEM: The system DiskGroup, used to store YFS's own metadata. YFS relies on this disk to complete startup and initialization.
- DG0: The data DiskGroup, used to store business data. The name of this DiskGroup can be configured by the user during installation; if not configured, the default value is DG0.

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


DiskGroup information includes:

- id: The globally unique ID of the DiskGroup, which will be referred to as dgid in FailureGroup or disk information.
- name: The name of the DiskGroup.
- type: The type of the DiskGroup, which can be either SYSTEM or USER.
  - SYSTEM: The system DiskGroup, used to store YFS's own metadata. Do not use it to store business data.
  - USER: The data DiskGroup, used to store business data files.
- level: The redundancy level of the DiskGroup. The redundancy level of each DiskGroup is independent.
  - 0: Indicates a redundancy level of EXTERNAL. Under this configuration, all data is stored as a single copy with no redundancy.
  - 1: Indicates a redundancy level of NORMAL. The system DiskGroup provides 3 replicas under this configuration. The data DiskGroup provides [2,3] YFS metadata replicas (the exact number depends on the number of FailureGroups) + 2 user data replicas under this configuration.
  - 2: Indicates a redundancy level of HIGH. The system DiskGroup provides 5 replicas under this configuration. The data DiskGroup provides [3,5] YFS metadata replicas (the exact number depends on the number of FailureGroups) + 3 user data replicas under this configuration.
- au_size: The size of the allocation unit, with the unit being bytes. It is the minimum dimension for YFS to allocate disk space.
- stat: The mount status of the DiskGroup.
  - MOUNTED: Indicates that the DiskGroup is mounted.
  - DISMOUNTED: Indicates that the DiskGroup is not mounted.
- block_size: The size of the file data block, with the unit being bytes.
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


Disk information includes:

- id: The globally unique ID of the disk.
- interid: The unique ID of the disk within the DiskGroup.
- name: The name of the disk.
- status: The status of the disk, including:
  - NORMAL: The disk is in normal status
  - OFFLINE_SYNC: A temporary state during the disk online process — the disk is writable but not readable. After online completion, the disk becomes normal.
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
    arch_files
    dbfiles
    redo11
    redo12
    redo13
    redo21
    redo22
    redo23
    system
    sysaux
    temp
    swap
    users
    undo1
    undo2
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
SELECT ID,NAME,TYPE,AU_SIZE,REDUNDANCY FROM V$YFS_DISKGROUP;

          ID NAME                              TYPE                                   AU_SIZE REDUNDANCY
------------ --------------------------------- --------------------------------- ------------ -----------------
           0 SYSTEM                            SYSTEM                                 1048576 NORMAL
           1 DG0                               USER                                   1048576 EXTERNAL

SELECT NAME,GROUP_NUMBER FROM V$YFS_FAILGROUP;
NAME                              GROUP_NUMBER
--------------------------------- ------------
SDG0_0                                       0
DG0_0                                        1
```

    == Monitor DiskGroup status and available capacity

```sql
SELECT NAME,TYPE,STATE,FREE_MB,USABLE_FILE_MB FROM V$YFS_DISKGROUP;

NAME                              TYPE                              STATE                           FREE_MB        USABLE_FILE_MB
--------------------------------- --------------------------------- ----------------- --------------------- ---------------------
SYSTEM                            SYSTEM                            MOUNTED                            9978                  9978
DG0                               USER                              MOUNTED                          100048                100048
```

    == Retrieve disk information

```sql
SELECT NAME,PATH,GROUP_NUMBER,PARTNERS,FREE_MB,READ_TIME,WRITE_TIME FROM V$YFS_DISK;

   GLOBAL_ID NAME             PATH                  GROUP_NUMBER PARTNERS          FREE_MB             READ_TIME          WRITE_TIME
------------ ---------------- --------------------- ------------ ----------------- ------------------- ------------------ -----------------
           0 SYSTEM_0         /dev/yfs/sys0         0            1 2                              9978                  0                 0
           1 SYSTEM_1         /dev/yfs/sys1         0            2 0                              9978                  0                 0
           2 SYSTEM_2         /dev/yfs/sys2         0            0 1                              9978                  3                 0
           3 DG0_0            /dev/yfs/data0        1                                           100048                  5                62
```

    :::

## Data DiskGroup Expansion

As the core data storage and coordination component of YAC or distributed clusters, insufficient YFS storage capacity can lead to sluggish business system responses, while inadequate redundancy increases data security risks. Therefore, when storage space alerts (error code YAS-05530) occur, redundancy configurations fail to meet the security standards for new business data, or new data-intensive business modules are added, the need to expand the data DiskGroup becomes urgent.

Before and after expansion, the disk group must meet the following configuration requirements:



- The number of disks under each FailureGroup within the same DiskGroup must remain consistent.  

- The available capacity of all disks under each FailureGroup within the same DiskGroup must be identical.

- A disk can belong to only one FailureGroup and cannot be specified multiple times.

- Each FailureGroup should, as much as possible, achieve [fault isolation](../../../Product Concepts/YAC Infrastructure/Yashan File System.md#FailureGroup).



In practice, users can choose one of the following approaches based on their needs:

|Expansion Options |Constraints  |Pros and Cons  |
| ---------- | ------ | ----------- |
| Add disks to the existing DiskGroup(s)   |  The same number of disks must be added to each FailureGroup within the DiskGroup.   | Pros: No adjustment is required for the storage locations of existing database files (i.e., business "unaware").     |
| Add FailureGroups to the existing DiskGroup(s)   | The number of new FailureGroups must be an integer multiple of the number of replicas specified by the DiskGroup's redundancy level.  | Pros: No adjustment is required for the storage locations of existing database files (i.e., business "unaware"). <br/><br/>  Cons: Based on the fault isolation principle between the new FailureGroups and existing FailureGroups, actual deployment requires adding the corresponding number of storage devices, which incurs higher hardware costs.          |
| Add new DiskGroup(s)   | No special constraints.   | Pros: Can configure a reasonable redundancy level for each new DiskGroup based on the security standards and hardware utilization requirements of the new business data. <br/><br/>Cons: Requires manual adjustment of the storage locations for new data to the new DiskGroup(s) (i.e., business "aware").  |

Expansion operations never affect existing data (including redundancy level and storage location).

### Prerequisites

The prerequisites for disk replacement (i.e., preparations for the new disk) are as follows:



    - The I/O fencing mode used by YAC has been confirmed. This can be viewed using the `ycsctl show fence` command.

    - The storage device has been [prepared](../../../Installation and Upgrade/Installation and Deployment/Pre-Installation Preparation/Preparing the Servers.md#Storage) according to requirements, and the hardware devices planned for each FailureGroup must meet the [fault isolation standard](../../../Product Concepts/YAC Infrastructure/Yashan File System.md#FailureGroup).

    - The storage device has been [configured](../../../Installation and Upgrade/Installation and Deployment/Pre-Installation Preparation/Configuring the Storage Devices) according to requirements (e.g., partitioning, mounting, etc.), and the disks must be bound under the same parent directory as the existing disk (e.g., `/dev/yfs`).

    - If [SCSI I/O Fencing](../../../Database Administration/Cluster Management/IO Fencing/SCSI IO Fencing) is used, it is recommended to first run the [fenceScsiCheck](../../../Database Administration/Cluster Management/IO Fencing/SCSI IO Fencing.md#fenceScsiCheck_usage) script to verify that the disks meet the requirements before creating the DiskGroup.





- The YASCS_HOME environment variable is set correctly.

- The YFS service has been started (you can check the YFS status using the `ycsctl status` command to confirm it is online).



### Add Disks to an Existing DiskGroup

1. Log in to the database installation server using the installation user.


2. Obtain the path information of the new disk.

    ```shell
    $ ll /dev/yfs
    ```

3. View the current configuration and related information of the target DiskGroup.

    ```shell
    $ yfscmd -D $YASCS_HOME
    YFSCMD > show diskgroup
    id name     type     level    au_size  stat     block_size  total_mb    free_mb     usable_file_mb
    0  SYSTEM   SYSTEM   1        1.00MB   MOUNTED  4096        3072        2397        799        
    1  DG0      USER     0        32.00MB  MOUNTED  4096        20480       16960       16960

    YFSCMD >  show failgroup
    id name     dgid
    0  SDG0_0   0
    1  DG0_0    1
    ```

    The name of the data DiskGroup and the name of its FailureGroup must be recorded for subsequent operations.

4. Execute the [ALTER DISKGROUP](../../../Tools Guide/yfscmd/Disk Management Commands.md#ALTERDG) command to add disks to the data DiskGroup.

    ```shell
    # This example demonstrates adding one disk to the FailureGroup DG0_0 of the DiskGroup DG0

    YFSCMD > exec ALTER DISKGROUP DG0 ADD FAILGROUP DG0_0 DISK '/dev/yfs/data1';
    ```
    If the target DiskGroup contains multiple FailureGroups, this operation must be repeated to add the same number of disks to each FailureGroup.

### Add FailureGroup(s) to an Existing DiskGroup

1. Log in to the database installation server using the installation user.


2. Obtain the path information of the new disk.

    ```shell
    $ ll /dev/yfs
    ```

3. View the current configuration and related information of the target DiskGroup.

    ```shell
    $ yfscmd -D $YASCS_HOME
    YFSCMD > show diskgroup
    id name     type     level    au_size  stat     block_size  total_mb    free_mb     usable_file_mb
    0  SYSTEM   SYSTEM   1        1.00MB   MOUNTED  4096        3072        2397        799        
    1  DG0      USER     0        32.00MB  MOUNTED  4096        20480       16960       16960

    YFSCMD >  show failgroup
    id name     dgid
    0  SDG0_0   0
    1  DG0_0    1
    ```

    The name and redundancy level of the data DiskGroup must be recorded for subsequent operations.

4. Execute the [ALTER DISKGROUP](../../../Tools Guide/yfscmd/Disk Management Commands.md#ALTERDG) command to add disks to the data DiskGroup.

    ```shell
    # The number of newly added FailureGroups must be an integer multiple of the number of replicas corresponding to the redundancy level

    YFSCMD > exec ALTER DISKGROUP DG0 ADD FAILGROUP DG0_1 DISK '/dev/yfs/data1';
    ```

5. View the FailureGroup list again.

    ```shell
    YFSCMD >  show failgroup
    id name     dgid
    0  SDG0_0   0
    1  DG0_0    1
    2  DG0_1    1
    ```

### Adding a New DiskGroup

1. Log in to the database installation server using the installation user.


2. Obtain the path information of the new disk.

    ```shell
    $ ll /dev/yfs
    ```
3. View the current list of DiskGroups.

    ```shell
    $ yfscmd -D $YASCS_HOME
    YFSCMD > ls
    SYSTEM
    DG0
    ```

4. Execute the [CREATE DISKGROUP](../../../Tools Guide/yfscmd/Disk Management Commands) command to add a new data DiskGroup.
    ```shell
    YFSCMD > exec CREATE DISKGROUP DG1 NORMAL REDUNDANCY 
    > FAILGROUP DG1_0 DISK '/dev/yfs/data10' NAME DG1_DISK10, '/dev/yfs/data11' NAME DG1_DISK11 
    > FAILGROUP DG1_1 DISK '/dev/yfs/data12' NAME DG1_DISK12, '/dev/yfs/data13' NAME DG1_DISK13;
    ```
5. View the DiskGroup list again.

    ```shell
    $ yfscmd -D $YASCS_HOME
    YFSCMD > ls
    SYSTEM
    DG0
    DG1
    ```

6. Plan storage paths for new business data reasonably; for details, please refer to [Logical Space Management](../Logical Space Management/00Logical Space Management).

## Data DiskGroup Failure Handling

Relying on the redundancy level attribute of the DiskGroup, as long as at least one available data replica remains, the database can continue to operate normally. However, DBAs should still regularly monitor the status of disks. Once a disk failure is detected, it is essential to promptly replace the faulty disk and rebuild redundancy to prevent the failure from spreading and affecting business operations.

In YFS, monitoring disk status can be done by examining the `status` field in the output of the `show disk` command or the `MOUNT_STATUS` field in [GV$YFS_DISK](../../../Reference Manual/System Views/Dynamic Performance Views/GV$YFS_DISK)/[V$YFS_DISK](../../../Reference Manual/System Views/Dynamic Performance Views/V$YFS_DISK) view. For specific viewing operations, please refer to [Viewing Disk Information](#Viewing).

### Replacing the Faulty Disk

When replacing a disk, the following rules must be followed:





- This operation cannot be performed on the system DiskGroup (named `SYSTEM`).

- The target DiskGroup to be operated on has been mounted.


- A single operation can only replace one offline disk.

- The new disk cannot be one that is already part of any DiskGroup.

- Each disk can have at most one pending repair-type operation task at a time, including: ONLINE, REPLACE.

- Each DiskGroup can have up to 64 repair-type operation tasks concurrently (either in progress or queued).

> **Note**:
> 
> - The essence of a disk replacement operation is to update the path of a disk in a DiskGroup and then re-online the disk. If a replacement task is interrupted (e.g., manually canceled), the disk path may have changed, and YFS will not automatically roll back this change. Manual intervention is required to re-initiate the replacement task to restore the original path.
>
> - When replacing a disk, if the disk path of the new disk is mistakenly specified as the original path of the disk named `disk_name`, YFS will not report an error or block the operation. In this case, the operation degrades to simply re-onlineing the target disk. 



#### Prerequisites

The prerequisites for disk replacement (i.e., preparations for the new disk) are as follows:



    - The I/O fencing mode used by YAC has been confirmed. This can be viewed using the `ycsctl show fence` command.

    - The storage device has been [prepared](../../../Installation and Upgrade/Installation and Deployment/Pre-Installation Preparation/Preparing the Servers.md#Storage) according to requirements, and the hardware devices planned for each FailureGroup must meet the [fault isolation standard](../../../Product Concepts/YAC Infrastructure/Yashan File System.md#FailureGroup).

    - The storage device has been [configured](../../../Installation and Upgrade/Installation and Deployment/Pre-Installation Preparation/Configuring the Storage Devices) according to requirements (e.g., partitioning, mounting, etc.), and the disks must be bound under the same parent directory as the existing disk (e.g., `/dev/yfs`).

    - If [SCSI I/O Fencing](../../../Database Administration/Cluster Management/IO Fencing/SCSI IO Fencing) is used, it is recommended to first run the [fenceScsiCheck](../../../Database Administration/Cluster Management/IO Fencing/SCSI IO Fencing.md#fenceScsiCheck_usage) script to verify that the disks meet the requirements before creating the DiskGroup.





- The YASCS_HOME environment variable is set correctly.

- The YFS service has been started (you can check the YFS status using the `ycsctl status` command to confirm it is online).



#### Operation Steps

1. Log in to the database installation server using the installation user.


2. Obtain the path information of the new disk.

    ```shell
    $ ll /dev/yfs
    ```

3. Obtain information about the faulty disk.

    ```shell
    $ yfscmd -D $YASCS_HOME
    YFSCMD >  show disk
    ```
    The name of the data DiskGroup to which the faulty disk belongs, the name and path of the faulty disk must be recorded for subsequent operations.

4. Execute the [ALTER DISKGROUP](../../../Tools Guide/yfscmd/Disk Management Commands.md#ALTERDG) command to replace the faulty disk with a new disk.

    ```shell
    # This example demonstrates replacing the disk DG1_DISK12 in the DiskGroup DG1, using /dev/yfs/data14

    YFSCMD > exec ALTER DISKGROUP DG1 REPLACE DISK DG1_DISK12  WITH '/dev/yfs/data14' POWER 10;
    ```
    You can specify the data migration intensity (the value of the `POWER` keyword) for the current plan based on business load. The higher the value, the greater the intensity and the faster the plan completes, but the higher the resource consumption.
    
5. Execute the following command to view the progress of this repair plan.

    ```shell
    YFSCMD > show job
    ```
    When the `status` field of the target plan changes to `FINISH`, it indicates that the plan has been successfully executed. Afterward, the faulty disk hardware can be removed.

### Re-Online a Disk

If a disk in normal status is mistakenly taken offline, it can be re-onlineed. The specific operation rules are as follows:





- This operation cannot be performed on the system DiskGroup (named `SYSTEM`).

- The target DiskGroup to be operated on has been mounted.


- A single operation can only re-online one offline disk.

- Each disk can have at most one pending repair-type operation task at a time, including: ONLINE, REPLACE.

- Each DiskGroup can have up to 64 repair-type operation tasks concurrently (either in progress or queued).



#### Prerequisites



- The YASCS_HOME environment variable is set correctly.

- The YFS service has been started (you can check the YFS status using the `ycsctl status` command to confirm it is online).



#### Operation Steps

1. Log in to the database installation server using the installation user.


2. Obtain the relevant information about the target disk.

    ```shell
    $ yfscmd -D $YASCS_HOME
    YFSCMD >  show disk
    ```
    The name of the data DiskGroup to which the target disk belongs and the name of the target disk must be recorded for subsequent operations.

3. Execute the [ALTER DISKGROUP](../../../Tools Guide/yfscmd/Disk Management Commands.md#ALTERDG) command to re-online the target disk.

    ```shell
    # This example demonstrates re-onlineing the disk DG1_DISK10 in the DiskGroup DG1

    YFSCMD > exec ALTER DISKGROUP DG1 ONLINE DISK DG1_DISK10 POWER 10;
    ```

    You can specify the data migration intensity (the value of the `POWER` keyword) for the current plan based on business load. The higher the value, the greater the intensity and the faster the plan completes, but the higher the resource consumption.
    
4. Execute the following command to view the progress of this repair plan.

    ```shell
    YFSCMD > show job
    ```
    When the `status` field of the target plan changes to `FINISH`, it indicates that the plan has been successfully executed.

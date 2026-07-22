*yfscmd* provides the `show` command to view the internal status of YFS. It currently supports viewing:

- DiskGroup information

- FailureGroup information

- Disk information

- YFS parameter information that can be modified online (i.e., Immediate Effect of Changes = Yes)

## show diskgroup

This command is used to list the DiskGroup information in YFS. Command format:

```shell
show diskgroup
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



## lsdg

This command is used to list the DiskGroup information in YFS, similar to `show diskgroup`. The command format is:

```shell
lsdg
```

## show failgroup

This command is used to list the FailureGroup information in YFS. Command format:

```shell
show failgroup
```


FailureGroup information includes:

- id: The globally unique ID of the FailureGroup, which will be referred to as fgid in disk information.
- name: The name of the FailureGroup.
- dgid: The ID of the DiskGroup to which this FailureGroup belongs.



## show disk

This command is used to list the disk information in YFS. Command format:

```shell
show disk
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



## lsdsk

This command is used to list the disk information in YFS, similar to `show disk`. The command format is:

```shell
lsdsk
```

## iostat

This command is used to list the read and write statistics of disks in YFS. The command format is:

```shell
iostat
```

Disk read and write statistics include:

- id: The globally unique ID of the disk.
- name: The name of the disk.
- group_number: The ID of the DiskGroup to which the disk belongs.
- reads: Total number of reads performed by the current instance on this disk.
- writes: Total number of writes performed by the current instance on this disk.
- read_bytes: Total number of bytes read by the current instance from this disk.
- written_bytes: Total number of bytes written by the current instance to this disk.
- read_time_us: Total read duration for this disk by the current instance, with the unit being μs.
- write_time_us: Total write duration for this disk by the current instance, with the unit being μs.
- path: The disk path.


## show job

This command is used to list information about disk repair plans. The command format is:

```shell
show job
```

Disk repair plan information includes:

- id: The globally unique ID of the plan.
- dgname: The name of the DiskGroup to which the plan belongs.
- oper: The type of the plan; currently always ONLINE, and disk replacement is also classified as ONLINE.
- disk: The target disk for the operation.
- power: The data migration intensity for the plan.
- status: The status of the plan.
  - IDLE: Indicates the plan is waiting to be executed.
  - RUNNING: Indicates the plan is currently executing.
  - FINISH: Indicates the plan has completed.
  - CANCEL: Indicates the plan has been canceled — either automatically due to an error (can be confirmed via `errno` and `errmsg`) or manually by the user.
- create_time: The creation time of the plan.
- end_time: The completion time of the plan. It is empty if the plan has not been completed yet.
- errno: Only meaningful when the plan encounters an error; the corresponding error code.
- errmsg: Only meaningful when the plan encounters an error; the corresponding error message.

<span id="show_param" name="show_param"></span>

## show param

This command is used to list the parameters in YFS that can be modified online (i.e., Immediate Effect of Changes = Yes).

Optional filtering condition `filter`, exact match or partial match, supports multiple conditions. Multiple filtering conditions are equivalent to `filter OR filter`, listing all results that match the filtering conditions.

Command format:

```shell
show param [filter [filter] ...] 
```

Parameter information includes:

- name: Parameter name.
- value: Parameter value.

## show status

This command is used to view the internal status of the YFS service.

> **Note**: 
> 
> This command is a laboratory feature, **for debugging only**.

Command format:

```bash
Instance
--------------------
home: /home/yashan/YASDB_NODE/node0

Topo
--------------------
  lock: 0
  id: 0
  master id: 0
  topoTerm: 0
  switchStatus: 0
  instCnt: 0
  aliveCnt: 0
  isWaitNodeJoin: 0
  yfsInstMap:   00000000
  ycsInstMap: 00000000
  topoVer: 0
  waitNodeJoinMap: 00000000
  buildingInstMap:  00000000
  thread:
    name: 
    startTime: 1970-01-01 12:01:00
    closed: 0
    status: COD_THREAD_STATUS_INIT

Shm
--------------------
max  : 1.52GB   
total: 64.00MB  
used : 35.11MB  
free : 28.89MB  
areas: 1
  curr size: 64.00MB  
  next size: 128.00MB 

HandlerPool
--------------------
count   = 1024
hwm     = 8
used    = 8
userCnt = 1

Mem
--------------------
max  : 256.00MB  
total: 1.00MB   
blocks:
  size : 64.00KB  
  count: 16

Hashmap
--------------------
DG0:
  capacity: 524288
  max load: 367001
  used    : 0
```

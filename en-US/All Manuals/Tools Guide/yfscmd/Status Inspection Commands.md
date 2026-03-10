*yfscmd* provides the `show` command to view the internal status of YFS. It currently supports viewing:

- diskgroup list

- failgroup list

- disk list

## show diskgroup

This command lists the diskgroup information in YFS. Command format:

```shell
show diskgroup
```

diskgroup information includes:

-  id: Globally unique ID of the diskgroup.
- name: diskgroup name.
- type: type, which can be:
  - SYSTEM: system diskgroup, managed by the cluster, do not use for data storage.
  - USER: regular diskgroup, created by users, stores data files.
- level: redundancy level
  - 0:  external
  - 1:  normal
  - 2:  high
- au_size: AU (Allocate Unit) size.
- stat: diskgroup status
  - MOUNTED: mounted
  - DISMOUNTED: not mounted
- block_size: file block size
- total_mb: total space
- free_mb: available space
- usable_file_mb: available file size, which is the diskgroup's available space minus multiple replicas

## lsdg
This command lists the diskgroup information in YFS, similar to `show diskgroup`. The command format is:

```shell
lsdg
```

## show failgroup

This command lists the failgroup information in YFS. Command format:

```shell
show failgroup
```

failgroup information includes:

-  id: Globally unique ID of the failgroup.
- name: Failgroup name.
- dgid: ID of the diskgroup to which the failgroup belongs.

## show disk

This command lists the disk information in YFS. Command format:

```shell
show disk
```

disk information includes:

- id: Globally unique ID of the disk.
- interid: Unique ID of the disk within the diskgroup.
- name: Name of the disk.
- status: Disk status, including NORMAL, ADDING, DROPPING and OFFLINE.
- fgid: ID of the failgroup to which the disk belongs.
- dgid: ID of the diskgroup to which the disk belongs.
- au_size: AU (Allocate Unit) size.
- au_count: AU (Allocate Unit) count.
- total_mb: Total space size.
- free_mb: Free space size.
- redundancy: The redundancy of diskgroup, EXTERNAL, NORMAL, HIGH.
- path: Disk path.
- partners: List of global IDs of partner disks, separated by spaces.

## lsdsk
This command lists the disk information in YFS, similar to `show disk`. The command format is:

```shell
lsdsk
```

## iostat
This command lists the read and write statistics of disks in YFS. The command format is:

```shell
iostat
```

Disk read and write statistics include:
- id: Globally unique ID of the disk.
- name: The name of the disk.
- group_number: The ID of the disk within the associated Diskgroup.
- reads: Number of read requests.
- writes: Number of write requests.
- read_bytes: Number of bytes read.
- written_bytes: Number of bytes written.
- read_time_us: Time taken to read.
- write_time_us: Time taken to write.
- path: Disk path.

<span id="show_param" name="show_param" class="yaslink"></span>

## show param

This command is used to list the parameters in YFS that can be modified online (i.e., Immediate Effect of Changes = Yes).

Optional filtering condition `filter`, exact match or partial match, supports multiple conditions. Multiple filtering conditions are equivalent to `filter OR filter`, listing all results that match the filtering conditions.

Command format:

```shell
show param [filter [filter] ...] 
```

param information includes:

- name: parameter name.
- value: parameter value.

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

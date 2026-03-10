*yfscmd* provides the `show` command to view the internal status of YFS. It currently supports viewing:

-  diskgroup list.
- failgroup list.
- disk list.

## show diskgroup

This command lists the diskgroup information in YFS. Command format:

```shell
show diskgroup
```

diskgroup information includes:

-  id: globally unique identifier.
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

## show failgroup

This command lists the failgroup information in YFS. Command format:

```shell
show failgroup
```

failgroup information includes:

-  id: globally unique identifier.
- name: name.
- dgid: belonging diskgroup id.

## show disk

This command lists the disk information in YFS. Command format:

```shell
show disk
```

disk information includes:

-  id: globally unique identifier.
- name: name.
- status: state, NORMAL, ADDING, DROPPING.
- fgid: belonging failgroup id.
- dgid: belonging diskgroup id.
- au_size: AU (Allocate Unit) size.
- au_count: number of disk AUs.
- path: disk path.

<span id="show_param" name="show_param" class="yaslink"></span>

## show param

This command is used to list the parameters in YFS that can be modified online (i.e., Immediate Effect of Changes = Yes).

Optional filtering condition `filter`, exact match or partial match, supports multiple conditions. Multiple filtering conditions are equivalent to `filter OR filter`, listing all results that match the filtering conditions.

Command format:

```shell
show param [filter [filter] ...] 
```

param information includes:

-  name: parameter name.
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

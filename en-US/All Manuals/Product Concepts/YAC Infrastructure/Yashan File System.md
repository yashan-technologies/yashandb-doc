The YFS (Yashan File System) is a dedicated parallel file system for YashanDB, providing functionality such as storage device management, high availability storage, and file system interfaces.

A group of YFS-related threads running on each server in a cluster is called a YFS instance. The YFS instance runs in the same process (YCS process) as the YCS instance. In a production environment, typically only one YFS instance runs on each server.

![yfs_instance.png](./image/yfs_instance.png)

The core server process, YASDB, acts as the client for the YFS instance, communicating only with the YFS instance on the same server. The YASDB process initiates metadata change requests to the YFS instance through a Unix Domain Socket, which updates the metadata buffer in shared memory and persists the metadata changes to shared storage. The YASDB process reads metadata from shared memory and performs read/write operations directly on the disk without going through the YFS instance, achieving I/O performance close to direct reads/writes on raw devices.

The YFS instance manages global storage metadata, ensuring the atomicity of operations through transactions. Metadata is synchronized across YFS services via a network, and consistency protocols guarantee global consistency, ensuring that the file states seen by each YFS instance are consistent.

## Disk Management

YFS has disk management capabilities, dividing raw disk devices into logical concepts such as disk groups and failure groups, managed in a hierarchical structure.

![yfs_dg_hierarchy](./image/yfs_dg_hierarchy.png)

### Disk Group (DiskGroup)

The disk group (DiskGroup) is the top-level logical unit for managing disk devices in YFS, and a DiskGroup should contain at least one FailureGroup.

There can be multiple DiskGroups in YFS, each managing different disks, with resource isolation and fault isolation between DiskGroups.

Different DiskGroups can implement differentiated storage management by specifying different configurations, such as configuring different numbers of replicas for different DiskGroups.

The attributes of each DiskGroup are specified at creation and cannot be changed afterward. The main attributes of a DiskGroup include redundancy (Redundancy Level) and AU size.

### Failure Group (FailureGroup)

YFS classifies disks that may fail simultaneously into failure groups called FailureGroups, using "multiple replicas" to achieve redundancy for data (including metadata and file data), supporting high availability of storage.

Each FailureGroup contains one or more disks, which may be:

- Some LUNs in the same shared storage
- Disks in the same cabinet
- Multiple storage devices powered by the same power supply
- Multiple storage devices in the same data center

There is no absolute standard for defining FailureGroups; rather, under specific operational conditions, disks are grouped appropriately based on the correlation of their failure probabilities, ensuring that different FailureGroups are unlikely to fail simultaneously.

YFS selects one disk from each failure group to form a partner disk set, which can be obtained through the `PARTNERS` field of the [V$YFS_DISK](../../Reference Manual/System Views/Dynamic Performance Views/V$YFS_DISK) view.

> **Note**:
>
> After adding or removing disks from a disk group, the disk partner relationships may change.

### Disk Devices (Disk)

All data in YFS is stored on disks according to specified policies, including YFS's own metadata, metadata for YFS files and directories, and user data.

A minimally functional YFS requires the following two disks:

- 1 BOOT_DISK: Used to store YFS's own metadata, with a recommended capacity of over 500MB. YFS relies on this disk to complete startup and initialization, and users can specify this disk in the YFS configuration.
- 1 user data disk: This is the data disk for YFS, and multiple data disks can be configured to store user data and must be specified when creating the disk group.

YFS only supports Direct IO mode for read and write operations, and the disks added to YFS must support DIO mode read and write with a block size of 512B - 64MB. The same disk cannot be reused in YFS to avoid potential data loss.

### High Availability of Storage

When creating a DiskGroup, the redundancy can be configured by specifying the number of replicas for that DiskGroup. Data copies will be stored in different FailureGroups; as long as at least one replica is intact, the data will be available.

Faults among disks in different FailureGroups are independent events, with a very low probability of simultaneous faults, thus significantly reducing the probability of data corruption and ensuring high availability of data.

#### Multiple Replicas

The number of replicas refers to how many copies of data are stored. For instance, one replica indicates that there is only one copy of data in YFS, while three replicas indicate there are three identical copies in YFS.

The number of replicas can be divided into user data copy counts and YFS metadata copy counts. When the number of FailureGroups exceeds the number of data copies, the system will automatically create additional replicas for the metadata, further improving the reliability of YFS.

Using multiple replicas to protect data requires proper planning of FailureGroups to ensure that the failure probabilities between data copies are independent:

- All replicas should be distributed across different FailureGroups, thus requiring `Number of FailureGroups >= Number of User Data Copies`.
- The number of YFS metadata copies at each redundancy level is a recommended value. If the number of FailureGroups is less than the required number of metadata copies, the number of metadata copies will be adjusted to equal the number of FailureGroups.

YFS stores N replicas of the same data across N disks within the same partner disk set, effectively ensuring that each replica is completely isolated across different FailureGroups. If the total number of disks in a group, along with its partner disks, is less than the number of replicas required by the disk group redundancy, that set of disks is considered unavailable, and additional disks or FailureGroups must be added to reach the required number of replicas before it can be used.

> **Note**: 
>
> Data replicas in YFS cannot span across DiskGroups, as the resources of each DiskGroup are fully isolated.

#### Redundancy (Redundancy)

YFS supports three levels of redundancy:

- EXTERNAL: This level indicates no data redundancy, with user data copy count of 1 and YFS metadata copy count of 1. The reliability of the data at this level depends on external capabilities such as RAID.
- NORMAL: General level of data redundancy, with user data copy count of 2, and a minimum of 2 to a maximum of 3 for YFS metadata copies.

- HIGH: High level of data redundancy, with user data copy count of 3, and a minimum of 3 to a maximum of 5 for YFS metadata copies.

#### Allocate Unit (Allocate Unit)

YFS divides disks into equal-sized allocate units (AU) for management. The AU size is the minimum unit of disk space allocation in YFS.

The supported AU size granularity includes: 1M (default), 4M, 8M, 16M, and 32M. Users can specify the AU size of the DiskGroup when creating it or use the default AU size.

The AU size determines the granularity of space management in YFS. A larger AU size allows YFS to create larger files and achieve better sequential I/O performance, but it also consumes more memory and wastes some disk space. Users should choose an appropriate AU size based on business characteristics, as an inappropriate AU size may impact YFS's operating performance. Recommended configurations for AU size include:

- If the system primarily handles small files, a smaller AU size is recommended.
- If the system primarily handles large files, a larger AU size is recommended.

- Better performance can be obtained when AU size is greater than or equal to the vast majority of I/O sizes.

## File and Directory Management

YFS provides a set of file operation APIs that abstract the details of underlying storage management. The semantics of these APIs are compatible with most file system operations, incorporating basic file system concepts such as files, directories, and paths.

YFS also provides a dedicated management terminal, *yfscmd*, offering a file management interface similar to the Linux Shell, including common commands like cd, ls, mv, cp, etc.

### Directory

YFS organizes and manages files in the form of a directory tree. The path structure of YFS is `+DG_NAME/DIR/PATH/file.name`.

The root of YFS is `+`, the first-level directory is the name of the disk group, and the subsequent directories are those of general directories and file names. The disk group directory is a virtual directory and cannot be modified directly through additions or deletions; modifications must be made indirectly through disk group additions or deletions.

Files can be identified by the file system type in absolute paths; those starting with `+` indicate a YFS path, while those starting with `/` indicate a local system path. The `yfscmd cp` command recognizes the filesystem based on the root path character to achieve cross-copying between YFS and local file systems.

### File

In addition to user data, YFS files contain some abbreviated attribute information, such as creation time and file size. Since YFS only supports direct I/O read and write operations, the file sizes in YFS are all multiples of 512 bytes.

The actual disk space occupied by a file may be greater than its file size, approximately `Round(FileSize/AuSize) * Redundancy`, and additional space is also occupied by the file's metadata, although this amount is relatively small.
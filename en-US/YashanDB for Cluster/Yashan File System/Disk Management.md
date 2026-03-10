Users can use the [yfscmd](../../Tools Guide/yfscmd/00yfscmd) tool for YFS disk management, including management of diskgroup, failuregroup, and disk.

Prerequisites:

- The YFS service is running (check with ycsctl status).
- The `YASCS_HOME` environment variable is set correctly.

## Creating the First Diskgroup

During the installation of YashanDB, the following diskgroups are automatically created:

- SYSTEM diskgroup, dedicated to YFS, please do not modify.
- User diskgroup, used to store user data. The name of this diskgroup can be configured by the user during installation. The default value if not configured is DG0. After the database installation is complete, this diskgroup already contains information related to the database instance. Please do not clean this diskgroup on your own, as doing so will result in the loss of all data.

```shell
$ yfscmd ls
SYSTEM
DG0
```

> **Caution**:
>
> This diskgroup contains information about the database instance created by default. If this diskgroup is accidentally cleaned, the database instance will fail and must be rebuilt.

## Viewing Diskgroup

Execute the following command to see the currently created diskgroups.

```shell
# View diskgroup on the default instance server
$ yfscmd -D $YASCS_HOME ls
SYSTEM
DG0

# View diskgroup on a specified instance server, -D specifies the server HOME
$ yfscmd -D /home/yashan/ycs/ce-1-2 ls
SYSTEM
DG0
```

All diskgroups are virtualized under the YFS root directory `+` as a subdirectory. Thus, you can view the list of diskgroups and their files and directories using the subcommand `ls` of `yfscmd`.

```shell
$ yfscmd -D $YASCS_HOME ls
SYSTEM
DG0
$ yfscmd -D $YASCS_HOME ls DG0
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

## Continuing to Create Diskgroup

In addition to the system-initialized diskgroup (named DG0), users can add their own diskgroups.

```shell
$ yfscmd

# Create a diskgroup and specify the failuregroup
YFSCMD >  exec CREATE DISKGROUP DG1 NORMAL REDUNDANCY
> FAILGROUP FG11 DISK '/dev/DISK_NAME1' NAME disk1, '/dev/DISK_NAME2' NAME disk2
> FAILGROUP FG12 DISK '/dev/DISK_NAME3', '/dev/DISK_NAME4';

> ls
SYSTEM
DG0
DG1

# Create a diskgroup without specifying failuregroup
# The system automatically creates a failuregroup for each disk
YFSCMD >  exec CREATE DISKGROUP DG2 EXTERNAL REDUNDANCY DISK '/dev/DISK_NAME5';

> ls
SYSTEM
DG0
DG1
DG2
```

> **Warn**:
>
> - Disks can only be specified as ordinary data disks, excluding YCR disks (controlled by the YCR_DISK parameter) and YCS voting disks (controlled by the VOTING_DISK parameter), otherwise it may cause system exceptions and crashes.
>
> - The same disk cannot be added multiple times to YFS. When the same disk is incorrectly configured to multiple diskgroups or failgroups, it can result in system crashes, data loss, and other critical failures.

## Modifying Diskgroup

Users can perform operations such as unmounting, mounting, and adding failuregroups to an existing diskgroup.

**1. Unmounting and Mounting Diskgroup**

```shell
# Unmount diskgroup
$ yfscmd -D $YASCS_HOME exec "ALTER DISKGROUP DG1 dismount"

# Mount diskgroup
$ yfscmd -D $YASCS_HOME exec "ALTER DISKGROUP DG1 mount"
```

> **Caution**: 
>
> After unmounting a diskgroup, the data on it cannot be operated on, and no modifications can be made. It can only be accessed after remounting.

**2. Adding Failuregroup**

YFS supports creating failuregroups at the time of diskgroup creation, as well as adding failuregroups to existing diskgroups.

```shell
# Add failuregroup, specify name
$ yfscmd -D $YASCS_HOME exec "ALTER DISKGROUP DG1 ADD FAILGROUP FG13 DISK '/dev/DISK_NAME6'"

# Add failuregroup without specifying a name, the system will automatically create a failuregroup for each disk
$ yfscmd -D $YASCS_HOME exec "ALTER DISKGROUP DG1 ADD DISK '/dev/DISK_NAME7'"
```

## Deleting Diskgroup

Before deleting a diskgroup, please ensure that the data in it is no longer needed.

When there is data present in the diskgroup, the `INCLUDING CONTENTS` option must be specified to successfully delete it.

The operation of deleting a diskgroup is irreversible, and all data will be erased, so please proceed with caution.

```shell
# Delete a diskgroup with no data, deletion will fail if there is data
$ yfscmd exec "DROP DISKGROUP DG1"
# Delete diskgroup, clear data if present
$ yfscmd exec "DROP DISKGROUP DG1 INCLUDING CONTENTS"
```

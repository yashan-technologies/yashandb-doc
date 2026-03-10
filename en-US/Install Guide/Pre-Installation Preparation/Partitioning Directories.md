## Server Directory Planning

Before installing the YashanDB server, the following directories need to be planned, and permissions must be granted to the installation user.

The installation user must have full privileges on all the directories mentioned below.

### Installation Directory

YashanDB adopts a cluster multi-server parallel installation model. Only one server needs to create the installation directory for downloading and unpacking the software package.

In this installation manual, the installation directory is planned to be under /home/yashan, and it will be created by the yashan user during [Software Package Download](Downloading Software Packages.html#Downloadingpackage).

### HOME Directory and DATA Directory

All YashanDB instance nodes must plan for HOME and DATA directories, and the installation user must possess all privileges for the corresponding directories.

- **HOME Directory**: The product directory for YashanDB, used to store the commands provided by YashanDB, libraries required for database operation, and various key components. In the installation operation, this directory will serve as the value of the --install-path parameter. After installation, YashanDB will generate $YASDB_HOME in this directory according to the rules.

- **DATA Directory**: The data directory for YashanDB, used to store various system data files of the database, some log files (please refer to [Log Management](../../All Manuals/Database Administration/Log Management/00Log Management) for details), and configuration files. User data is also stored in this directory by default. However, for YACs, all data files and redo files must be stored on shared storage, and the DATA directory will only be used to store instance runtime-related configuration files, log files, and other data. In the installation operation, this directory will serve as the value of the --data-path parameter. After the installation, YashanDB will generate $YASDB_DATA in this directory according to the rules.

    > **Note**:
    >
    > If the DATA directory path is too long, it may affect the normal operation of certain features, such as [UDS local connections](../../All Manuals/产品安全/连接管理/00连接管理) and other UDS-based features. It is recommended that the path length does not exceed 64 bytes.

In this installation manual, the sample values for HOME and DATA directories are planned under /data/yashan. You can execute the following commands to create the corresponding paths and grant permissions:

```shell
# Create /data/yashan directory
# mkdir -p /data/yashan

# Direct permission grant based on the parent directory
# chown -R yashan:yashan /data/yashan

# Create HOME directory, the complete HOME directory is /data/yashan/yasdb_home
# su yashan
$ cd /data/yashan
$ mkdir yasdb_home

# Create DATA directory, the complete DATA directory is /data/yashan/yasdb_data
$ mkdir yasdb_data
```

<span id="run_log_path" name="run_log_path" class="yaslink"></span>

### Run Log Directory

The run log directory of YashanDB is used to store log files generated during the database operation, including run.log and slow.log of the database, as well as logs from yasom and yasagent.

To avoid interference between run logs and data file IO, it is recommended to configure the database run log directory on a different physical disk than the DATA directory. Additionally, the run log directory can be planned according to the following scenarios:

- Unified storage according to planning

    1. Plan a directory to store run.log, slow.log, and logs from yasom and yasagent.
    
    2. In the installation operation, this directory will serve as the value of the --data-path parameter.

- Separated storage according to planning

    1. Plan different directories to separately store run.log, slow.log, and logs from yasom and yasagent.
    
    2. In the installation operation, you can use one of these directories (e.g., the planned directory for yasom and yasagent logs) as the value of the --log-path parameter. After generating the [configuration file](../../All Manuals/Tools Guide/yasboot/Configuration Files/00Configuration Files){cluster_name}.toml, modify the parameters related to the other two types of log directories as needed before executing the subsequent installation deployment operations. Alternatively, you can modify the corresponding [configuration parameters](../../All Manuals/Reference Manual/Configuration Parameters) to adjust the storage locations of run.log and slow.log after the installation is completed.

- Use default mechanism

    - If the run log directory is not planned, YashanDB will create a subdirectory log under the HOME directory by default (according to the example in this manual, it would be /data/yashan/yasdb_home/log) to store run.log, slow.log, as well as logs from yasom and yasagent.
    
    - In the installation operation, there is no need to specify the --log-path parameter.

<span id="lun" name="lun" class="yaslink"></span>

## Bind LUN (YAC Deployment)

This step is only applicable to the YAC Deployment scenario using shared storage. Other scenarios should be skipped.

The actual deployment environment may differ from the example. Please adjust according to the example, and do not use the following configurations directly in a production environment. For primary/standby cluster deployment, LUNs must be planned and bound separately for the master and standby clusters.

YashanDB requires that shared storage be planned with the following two types of disks, and all disks should be bound to the same directory, which will serve as the value of the --disk-found-path parameter. This document uses the default value `/dev/yfs` as an example.

- **System Disk**
  
    Used for cluster management, only supporting 1, 3, or 5 disks of the same size, with each disk having a minimum capacity of 1G (if the number of system disks is 1 and the AU Size is configured to 32M, the size of the system disk must be at least 2G). The absolute path of these disks will serve as the value of the --system-data parameter, separated by commas, such as `--system-data /dev/yfs/sys0,/dev/yfs/sys1,/dev/yfs/sys2`. The number of planned system disks will affect the redundancy of the system disk group, with 1, 3, or 5 system disks corresponding to EXTERNAL, NORMAL, or HIGH redundancy.

- **Data Disk**

    Used to store cluster business data, it can be configured with 1 or more disks of the same size. The quantity and capacity should be planned according to the actual business needs. The absolute path of these disks will serve as the value of the --data parameter, separated by commas, such as `--data /dev/yfs/data0,/dev/yfs/data1,/dev/yfs/data2`.

This document does not provide operational guidance for networking and storage partitioning of shared storage. Administrators should follow the installation instructions provided by the shared storage vendor. This document assumes 3 system disks + 1 data disk, with 4 LUNs already partitioned on the shared storage and mounted via multipath, planned as follows:

|LUN |Disk Type |Planned Binding Path (Example Value) |
|------------|--------|--------------------------------------|
| /dev/mapper/lun03-sys0 | System Disk | /dev/yfs/sys0 |
| /dev/mapper/lun04-sys1 | System Disk | /dev/yfs/sys1 |
| /dev/mapper/lun05-sys2 | System Disk | /dev/yfs/sys2 |
| /dev/mapper/lun01-data0 | Data Disk | /dev/yfs/data0 |

The binding directory (/dev/yfs) and disk names (data0, sys0, etc.) are example values. Please adjust according to actual business needs, but keep in mind:

- The absolute path length of the bound disk (e.g., `/dev/yfs/sys0`) must not exceed 31 bytes.
- Both the binding directory and the absolute paths of the disks will be used as corresponding parameter values in the `yasboot package ce gen` command. If there are modifications, please use the actual values when executing `yasboot package ce gen`.

### First Binding

This document will introduce two methods: multi-path binding (recommended) and SCSI device WWID binding. The main differences are:

|No. |Step |Description |
|------------|--------|--------------------------------------|
| 0   | Prerequisites       | When binding storage devices via SCSI device WWID, the WWIDs of all devices under the target path (e.g., /dev) must be unique; otherwise, binding will fail. |
| 1   | Step 1: Generate Configuration File | The contents of the genDevRuleByUUID.sh script differ for the two methods, thus the generated configuration file yashan-device-rule.rules will also differ. |
| 2   | Step 4 (Optional): Modify Multi-path Configuration File | If using multipath binding and needing [IO Fencing based on SCSI persistent reservations](../../All Manuals/YashanDB for Cluster/Yashan Cluster Service/IO Fencing/SCSI IO Fencing) functionality, this functionality must be modified in the multipath configuration file on each server of YAC before use. |

>**Caution**:
>
> Binding storage devices through SCSI device WWID does not meet high availability requirements and is not recommended for production environments.

Please choose the appropriate method and execute the corresponding operations to bind LUNs as the root user on all servers in YAC.

<span id="first" name="first" class="yaslink"></span>

::: tabs

== Method One: Multipath Binding

#### Step 1: Generate Configuration File

1. Log in to the server as the root user.

2. Create a script named `genDevRuleByUUID.sh` in the root directory:

```shell
# cd ~
# vi genDevRuleByUUID.sh
```

3. Input the following content and modify `DEVICES` to the paths of each device, change `TARGETS` to the binding names of each device, ensuring names correspond to device paths, and modify `YFS_DISK_DIR` to the binding directory. Save and exit.


```shell
#/bin/bash

# Please change to the paths of each device
DEVICES=("/dev/mapper/lun01-data0" "/dev/mapper/lun03-sys0" "/dev/mapper/lun04-sys1" "/dev/mapper/lun05-sys2")
# Please specify the names for binding these devices in the same order as the DEVICES array, ideally related to the business
TARGETS=("data0"                   "sys0"                   "sys1"                   "sys2")
# Specify the binding directory
YFS_DISK_DIR="yfs"

# Keep default; ensure the yashan user exists
USER="yashan"
GROUP="yashan"
UGROUP="$USER:$GROUP"

if [ ${#TARGETS[@]} != ${#DEVICES[@]} ]
then
    echo "DEVICES and TARGETS count does not match"
    exit 1
fi

if [ ${#TARGETS[@]} == 0 ]
then
    echo "no targets found"
    exit 1
fi

curDir=$(cd "$(dirname "$0")";pwd)
rm -f $curDir/yashan-device-rule.rules

count=${#TARGETS[@]}

for ((i = 0; i < $count; i ++))
do
    device=${DEVICES[$i]}
    target_name=${TARGETS[$i]}
    target="${YFS_DISK_DIR}/${target_name}"
    
    str1="$device:`udevadm info --query=all --name=$device | grep DM_UUID`"
    str2=${str1#*=}
    echo "KERNEL==\"dm-*\",ENV{DM_UUID}==\"${str2}\",SYMLINK+=\"$target\",OWNER=\"$USER\",GROUP=\"$GROUP\",MODE=\"0666\",OPTIONS:=\"nowatch\",RUN+=\"/bin/sh -c 'chown -R $UGROUP $target'\""  >> $curDir/yashan-device-rule.rules
done
```

4. Execute the script to generate the configuration file:

```shell
# sh ./genDevRuleByUUID.sh
```

Upon execution, a binding configuration file named yashan-device-rule.rules will be generated at the current path, with content as follows, though it may differ in actual deployment:



```shell
# cat yashan-device-rule.rules
KERNEL=="dm-*",ENV{DM_UUID}=="mpath-36d039ea000a2231f0000e7d9668d7b4a",SYMLINK+="yfs/data0",OWNER="yashan",GROUP="yashan",MODE="0666",OPTIONS:="nowatch",RUN+="/bin/sh -c 'chown -R yashan:yashan /dev/yfs/data0'"
KERNEL=="dm-*",ENV{DM_UUID}=="mpath-36d039ea000a2231f0000e7dd668d7b68",SYMLINK+="yfs/sys0",OWNER="yashan",GROUP="yashan",MODE="0666",OPTIONS:="nowatch",RUN+="/bin/sh -c 'chown -R yashan:yashan /dev/yfs/sys0'"
KERNEL=="dm-*",ENV{DM_UUID}=="mpath-36d039ea000a233b90000e358668d7caf",SYMLINK+="yfs/sys1",OWNER="yashan",GROUP="yashan",MODE="0666",OPTIONS:="nowatch",RUN+="/bin/sh -c 'chown -R yashan:yashan /dev/yfs/sys1'"
KERNEL=="dm-*",ENV{DM_UUID}=="mpath-36d039ea000a233b90000e35a668d7cbf",SYMLINK+="yfs/sys2",OWNER="yashan",GROUP="yashan",MODE="0666",OPTIONS:="nowatch",RUN+="/bin/sh -c 'chown -R yashan:yashan /dev/yfs/sys2'"
```

SYMLINK represents the alias for the bound device; check whether it matches the expected binding target.



#### Step 2: Bind Storage Devices

1. Move the configuration file to the /etc/udev/rules.d directory.

```shell
# cp yashan-device-rule.rules /etc/udev/rules.d/yashan-device-rule.rules
```

2. Load the udev configuration.

```shell
# udevadm control --reload-rules
```

3. Bind the storage devices.

```shell
## Scenario 1: Re-bind all devices in the rules file
# udevadm trigger

## Scenario 2: Re-bind a specific device in the rules file
# udevadm trigger --sysname-match=sdf
```

#### Step 3: Check Binding Result

1. Execute the following command to confirm all bindings have taken effect, with user and group both being `yashan`:

```shell
# ls -l /dev/yfs/*
lrw-rw-rw- 1 yashan yashan 7 Jul 11 14:44 data0 -> ../dm-5
lrw-rw-rw- 1 yashan yashan 7 Jul 11 14:44 sys0 -> ../dm-7
lrw-rw-rw- 1 yashan yashan 7 Jul 11 14:44 sys1 -> ../dm-4
lrw-rw-rw- 1 yashan yashan 7 Jul 11 14:44 sys2 -> ../dm-6
```

And verify that the user and group pointing to these devices are both `yashan`, and the privilege is `0666`:

```shell
# ls -l /dev/dm-4
brw-rw-rw- 1 yashan yashan 253, 4 Jul 11 14:44 /dev/dm-4

# Other devices are similar
```

2. On all servers in the cluster, execute the following command to confirm that **all** bound multipath WWIDs are consistent across servers.

```shell
# Compare the output results from each server for consistency
# /lib/udev/scsi_id --whitelisted  --device=/dev/yfs/data
# /lib/udev/scsi_id --whitelisted  --device=/dev/yfs/sys0
# /lib/udev/scsi_id --whitelisted  --device=/dev/yfs/sys1
# /lib/udev/scsi_id --whitelisted  --device=/dev/yfs/sys2
```
If the WWIDs corresponding to the same path on two servers do not match, it indicates that the mounted shared storage is not the same LUN, and you should contact the network administrator for resolution.


<span id="modify_multipath_conf" name="modify_multipath_conf" class="yaslink"></span>

#### Step 4 (Optional): Modify Multi-path Configuration File

When using multipath binding for storage devices, this optional step must be completed on each server in YAC before utilizing the [IO Fencing based on SCSI persistent reservations](../../All Manuals/YashanDB for Cluster/Yashan Cluster Service/IO Fencing/SCSI IO Fencing) functionality.

1. Confirm that the mpathpersist tool is installed.

```shell
# mpathpersist -h
```

2. Modify the /etc/multipath.conf file on each server, adding the "reservation_key file" item to the "defaults" section.

Example of non-complete file content, please add the options according to the description, and do not modify other configuration items.

```shell
defaults {
    reservation_key file
}
```

== Method Two: Bind via SCSI Device WWID

#### Step 1: Generate Configuration File

1. Log in to the server as the root user.

2. Create a script named `genDevRuleByUUID.sh` in the root directory:

```shell
# cd ~
# vi genDevRuleByUUID.sh
```

3. Input the following content and modify `DEVICES` to the paths of each device, change `TARGETS` to the binding names of each device, ensuring names correspond to device paths, and modify `YFS_DISK_DIR` to the binding directory. Save and exit.


```shell
#/bin/bash

# Please change to the paths of each device
DEVICES=("/dev/sdc" "/dev/sdd" "/dev/sde" "/dev/sdf")
# Please specify the names for binding these devices in the same order as the DEVICES array, ideally related to the business
TARGETS=("data0"     "sys0"    "sys1"     "sys2")
# Specify the binding directory
YFS_DISK_DIR="yfs"

# Keep default; ensure the yashan user exists
USER="yashan"
GROUP="yashan"
UGROUP="$USER:$GROUP"

if [ ${#TARGETS[@]} != ${#DEVICES[@]} ]
then
    echo "DEVICES and TARGETS count does not match"
    exit 1
fi

if [ ${#TARGETS[@]} == 0 ]
then
    echo "no targets found"
    exit 1
fi

curDir=$(cd "$(dirname "$0")";pwd)
rm -f $curDir/yashan-device-rule.rules

count=${#TARGETS[@]}

for ((i = 0; i < $count; i ++))
do
    device=${DEVICES[$i]}
    target_name=${TARGETS[$i]}
    target="${YFS_DISK_DIR}/${target_name}"
    
    echo "KERNEL==\"sd*\", SUBSYSTEM==\"block\", PROGRAM==\"/usr/lib/udev/scsi_id -g -u -d /dev/\$parent\", RESULT==\"`/usr/lib/udev/scsi_id -g -u -d $device`\",SYMLINK+=\"$target\",OWNER=\"$USER\", GROUP=\"$GROUP\", MODE=\"0666\", OPTIONS:=\"nowatch\", RUN+=\"/bin/sh -c 'chown -R $UGROUP /dev/$target'\"" >> $curDir/yashan-device-rule.rules
done
```

4. Execute the script to generate the configuration file:

```shell
# sh ./genDevRuleByUUID.sh
```

Upon execution, a binding configuration file named yashan-device-rule.rules will be generated at the current path, with content as follows, though it may differ in actual deployment:



```shell
# cat yashan-device-rule.rules
KERNEL=="sd*", SUBSYSTEM=="block", PROGRAM=="/usr/lib/udev/scsi_id -g -u -d /dev/$parent", RESULT=="36a01c8d100cd2c1b777f88dd000002b4",SYMLINK+="yfs/data0",OWNER="yashan", GROUP="yashan", MODE="0666", OPTIONS:="nowatch", RUN+="/bin/sh -c 'chown -R yashan:yashan /dev/yfs/data0'"
KERNEL=="sd*", SUBSYSTEM=="block", PROGRAM=="/usr/lib/udev/scsi_id -g -u -d /dev/$parent", RESULT=="36a01c8d100cd2c1b777f88be000002b3",SYMLINK+="yfs/sys0",OWNER="yashan", GROUP="yashan", MODE="0666", OPTIONS:="nowatch", RUN+="/bin/sh -c 'chown -R yashan:yashan /dev/yfs/data0'"
KERNEL=="sd*", SUBSYSTEM=="block", PROGRAM=="/usr/lib/udev/scsi_id -g -u -d /dev/$parent", RESULT=="36a01c8d100cd2c1b777f88be000002b5",SYMLINK+="yfs/sys1",OWNER="yashan", GROUP="yashan", MODE="0666", OPTIONS:="nowatch", RUN+="/bin/sh -c 'chown -R yashan:yashan /dev/yfs/data0'"
KERNEL=="sd*", SUBSYSTEM=="block", PROGRAM=="/usr/lib/udev/scsi_id -g -u -d /dev/$parent", RESULT=="36a01c8d100cd2c1b777f88be000002b6",SYMLINK+="yfs/sys2",OWNER="yashan", GROUP="yashan", MODE="0666", OPTIONS:="nowatch", RUN+="/bin/sh -c 'chown -R yashan:yashan /dev/yfs/data0'"
```



#### Step 2: Bind Storage Devices

1. Move the configuration file to the /etc/udev/rules.d directory.

```shell
# cp yashan-device-rule.rules /etc/udev/rules.d/yashan-device-rule.rules
```

2. Load the udev configuration.

```shell
# udevadm control --reload-rules
```

3. Bind the storage devices.

```shell
## Scenario 1: Re-bind all devices in the rules file
# udevadm trigger

## Scenario 2: Re-bind a specific device in the rules file
# udevadm trigger --sysname-match=sdf
```

#### Step 3: Check Binding Result

1. Execute the following command to confirm all bindings have taken effect, with user and group both being `yashan`:

```shell
# ls -l /dev/yfs/*
lrw-rw-rw- 1 yashan yashan 7 Jul 11 14:44 data0 -> ../dm-5
lrw-rw-rw- 1 yashan yashan 7 Jul 11 14:44 sys0 -> ../dm-7
lrw-rw-rw- 1 yashan yashan 7 Jul 11 14:44 sys1 -> ../dm-4
lrw-rw-rw- 1 yashan yashan 7 Jul 11 14:44 sys2 -> ../dm-6
```

And verify that the user and group pointing to these devices are both `yashan`, and the privilege is `0666`:

```shell
# ls -l /dev/dm-4
brw-rw-rw- 1 yashan yashan 253, 4 Jul 11 14:44 /dev/dm-4

# Other devices are similar
```

2. On all servers in the cluster, execute the following command to confirm that **all** bound multipath WWIDs are consistent across servers.

```shell
# Compare the output results from each server for consistency
# /lib/udev/scsi_id --whitelisted  --device=/dev/yfs/data
# /lib/udev/scsi_id --whitelisted  --device=/dev/yfs/sys0
# /lib/udev/scsi_id --whitelisted  --device=/dev/yfs/sys1
# /lib/udev/scsi_id --whitelisted  --device=/dev/yfs/sys2
```
If the WWIDs corresponding to the same path on two servers do not match, it indicates that the mounted shared storage is not the same LUN, and you should contact the network administrator for resolution.


:::

### Rebinding

If there is a need to rebind storage devices during the database operation, you can refer to the following steps:

1. Log in to the target server as the root user.

2. Check the current configuration rules (i.e., yashan-device-rule.rules file) to ensure that the file exists and contains the correct matching conditions and actions.

    ```shell
    # cat /etc/udev/rules.d/yashan-device-rule.rules
    ```

    - If the rules file is correct, directly proceed with the subsequent operations.

    - If the rules file is incorrect, you may refer to "Step 1: Generate Configuration File" in [Initial Binding](#first) to regenerate this file before proceeding with the subsequent operations.

3. Reload the udev configuration.

    ```shell
    # udevadm control --reload-rules
    ```

4. Rebind the storage devices.

    ```shell
    ## Scenario 1: Re-bind all devices in the rules file
    # udevadm trigger

    ## Scenario 2: Re-bind a specific device in the rules file
    # udevadm trigger --sysname-match=sdf
    ```

5. Check the device status.

    ```shell
    # udevadm info --query=all --name=/dev/sdf
    ```
## Mount NVMe Disk

This step is only applicable to the Distributed Cluster Deployment scenario. Other scenarios should be skipped.

The actual deployment environment may differ from the example. Please adjust according to the example, and do not use the following configurations directly in a production environment.

YashanDB requires that every DN in Distributed Clusterhas been mounted two NVMe disks like following:

- **System Disk(/dev/nvme0n1 will be a example)**
  
    Used for cluster management, with having a minimum capacity of 1G (if the AU Size is configured to 32M, the size must be at least 2G). The absolute path of this disk will serve as the value of the --system-data parameter.
	
- **Data Disk(/dev/nvme0n2 will be a example)**

    Used to store cluster business data. The capacity should be planned according to the actual business needs. The absolute path of these disks will serve as the value of the --data parameter.

Read and write privileges on thes NVMe disks should be granted to the database installation user:

```shell
# chown -R yashan:yashan /dev/nvme0n1
# chmod -R yashan:yashan /dev/nvme0n2
```

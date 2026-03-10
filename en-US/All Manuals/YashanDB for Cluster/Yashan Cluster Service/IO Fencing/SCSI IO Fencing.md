The SCSI Persistent Reservation (PR) based I/O Fencing method (hereinafter referred to as SCSI I/O Fencing) utilizes the SCSI Persistent Reservation commands, widely supported by shared storage devices, to construct I/O fencing for YACs, providing absolute safety and better RTO metrics.

This method requires the YCSRA process to be online to provide privilege operation proxy services. Please ensure that the YCSRA process has been started on each server. If not, please refer to the [ycsrootagent tool](../../../Tools Guide/ycsrootagent) to manually start it with sudo. To simplify operations, it is recommended to configure the process to start automatically on boot for each server. For detailed instructions, please refer to [Configuring Boot Autostart](../../../Installation and Upgrade/Installation and Deployment/Initial Environment after Installation/Configuring Boot Autostart).

- Type Code: 1

- Hardware Requirements: The shared storage device must support SCSI-3 protocol (or above) and SPC-3 command set (SCSI Primary Commands-3 or above), with support for type 5 persistent reservations (Write Exclusive – Registrants Only).

- Hardware Capability Detection Script: [fenceScsiCheck.sh](#fenceScsiCheck_usage).

- Notes: If shared storage is mounted via multipath, please [modify the multipath configuration file](../../../Installation and Upgrade/Installation and Deployment/Pre-Installation Preparation/Partitioning Directories.html#modify_multipath_conf) before configuring this method.

## Basic Principle

The PR command set mainly includes subcommands for registration, reservation, preemption, release, and query. Generally, the primary server of YAC performs registration and reservation, while other member servers also register, ensuring that only cluster member servers possess write privilege to the shared storage.

Before updating the cluster membership, the primary server will perform preemption to clear the key of the evicted server, preventing it from writing data to the shared storage. When the failed server recovers and rejoins the cluster, it will re-register and regain write privilege.

## Common Issues

#### After enabling SCSI I/O Fencing, *yfscmd* operations such as adding disks fail

- Error Code YAS-00304: The disk is not a SCSI block storage device, does not support SCSI-3 or above protocol, or does not support persistent reservation subcommands. Use the [fenceScsiCheck script](#fenceScsiCheck_usage) to further diagnose and verify.

- Error Code YAS-00341: The disk is a SCSI device but retains information from none this YAC registrations or reservations. Please use caution and confirm safety before using the [ycsrootagent scsi clear command](../../../Tools Guide/ycsrootagent.html#scsi_clear) to clear residual information.

<span id="fenceScsiCheck_usage" name="fenceScsiCheck_usage" class="yaslink"></span>

## Appendix: Hardware Capability Detection Script

The fenceScsiCheck.sh script can automatically detect whether specific storage devices meet the hardware requirements for SCSI I/O Fencing (supporting SCSI-3 protocol, SPC-3 command set, and type 5 persistent reservations).

### Usage Instructions

The fenceScsiCheck script is located in the `$YASDB_HOME/scripts` directory and must be executed under root (or sudo). It supports two modes: single-server detection and dual-server detection.

- Single-Server Detection: Detection is performed only on the server running the script, which checks whether the disks are SCSI block storage devices, whether they support the registration and reservation subcommands, and whether they support type 5 persistent reservations.

- Dual-Server Detection: Detection is performed on both the server running the script and the auxiliary server specified by the -i option. It checks whether the disks are SCSI block storage devices, whether they support all persistent reservation subcommands, and whether they support type 5 persistent reservations.

Typically, it is recommended to specify an auxiliary server using the -i option for dual-server detection to obtain more accurate and reliable results.

|Option |Meaning |
| ------------------ | ---------------------------------------------------- |
| *-d*                 | Disk device name being operated on, multiple device names separated by commas, must be absolute path, cannot be omitted. A single device name can have a maximum of 31 characters. |
| *-i*                 | Must specify the auxiliary server's IP address for dual-server detection. This address must be reachable via ping. |
| *-u*                 | Username for logging into the auxiliary server. If not specified, it will attempt to use the current executing user's account. This user must have sudo no-password privilege on the auxiliary server. |
| *-p*                 | Password for logging into the auxiliary server. If not specified, it attempts to log in without a password. It is recommended to configure no-password login for the user specified by -u. |

> **Note**: 
>
> To avoid exposing plaintext passwords, it is recommended to configure no-password login for the user specified by -u.
>
> If there is residual persistent reservation information on the disk specified by -d, this script will automatically clear it, so please ensure that the disk is free before executing the script.

### Dependencies

This script primarily relies on the [ycsrootagent tool](../../../Tools Guide/ycsrootagent), and in certain cases depends on the following tools:

- mpathpersist: When the server uses DM-Multipath software to manage shared storage, the script requires mpathpersist. Typically, the multipath-tools package is installed along with mpathpersist. You can check if the tool is available by running `mpathpersist --help`.

- sshpass: This tool is required for dual-server detection if the user specified by -u has not configured no-password login. You can check if the tool is available by running `sshpass -h`. It is generally recommended to configure no-password login for the user.

### Usage Example

When a YAC is configured with SCSI I/O Fencing, it is recommended for users to manually execute this script to check the hardware capability of the devices before using the [yfscmd disk management command](../../../Tools Guide/yfscmd/Disk Management Commands) to add new disks to the cluster file system.

This example checks the dual-server capabilities of disks /dev/yfs/DISK_NAME1 and /dev/yfs/DISK_NAME2, with the auxiliary test machine's IP being 192.168.1.123, and the login user being yashan. Please replace these with actual device paths, usernames, and their login methods during the operation.

```shell
$ sudo ./fenceScsiCheck.sh -d /dev/yfs/DISK_NAME1,/dev/yfs/DISK_NAME2 -i 192.168.1.123 -u yashan -p password
SCSI-based I/O Fencing Test(Dual-host) Start
Test Running on /dev/yfs/DISK_NAME1...
Device /dev/yfs/DISK_NAME1 Support SCSI-based I/O Fencing
Test Running on /dev/yfs/DISK_NAME2...
Warning: failed to register and reserve, maybe /dev/yfs/DISK_NAME2 is not scsi block device or not support persistent reservation subcommand
Supportive Device: /dev/yfs/DISK_NAME1
Unsupportive Device: /dev/yfs/DISK_NAME2
Test failed
```

In the detection results, `Supportive Device` will list the disk paths that meet the SCSI I/O Fencing hardware requirements, while `Unsupportive Device` will list the disk paths that do not meet these requirements. Please refer to the Warning information returned during the test for more details regarding the paths.

If the script detection results show that there are disks that do not meet hardware requirements, please replace them with compatible shared storage devices or consider using other types of I/O Fencing methods.
## Overview

The reservation-based I/O fencing method utilizes Persistent Reservation commands supported by shared storage devices to construct I/O fencing for YAC/distributed clusters, providing absolute safety and better RTO metrics.

I/O Fencing relies on the storage reservation mechanism, and YashanDB supports two standard solutions: SCSI Persistent Reservation from the SCSI protocol stack, and NVMe Reservation from the NVMe protocol stack.

### SCSI Persistent Reservation

SCSI Persistent Reservation (SCSI PR, SCSI Persistent Reservation) is a cluster access control and fault isolation mechanism defined by SCSI-3/SPC-3 and above protocols for multi-server shared block devices, and is the standard solution for traditional FC/iSCSI SAN storage to achieve high-availability clusters. The core workflow is as follows:

1. **Register**: The server sends the `PERSISTENT RESERVE REGISTER` command to the storage device, using a unique key (Reservation Key) to complete identity registration on the device, becoming a "Registrant".

2. **Reserve**: The server sends the `PERSISTENT RESERVE` command to apply for a specific type of access permission. In typical scenarios, Type 5 (Write Exclusive – Registrants Only) is used to only allow registered servers to perform write operations on the device, achieving access isolation.

3. **Preempt/Clear**: When a node fails, the surviving node can send the `PERSISTENT RESERVE PREEMPT` command to forcibly preempt and clear the failed node's reservation, preventing it from continuing to issue I/O requests, thereby completing I/O Fencing and ensuring data consistency.

### NVMe Reservation

NVMe Reservation is a cluster access control and fault isolation mechanism natively supported by NVMe 1.2 and above protocols for multi-server shared NVMe devices (PCIe direct connection / NVMe-oF), and is a native solution to replace SCSI PR under the NVMe architecture. The core workflow is as follows:

1. **Register**: The server sends the `Reservation Register` command to the NVMe Controller, using the server's unique Host ID to complete registration on the device.

2. **Reserve**: The server sends the `Reservation Acquire` command to apply for a specific type of reservation permission, such as Write Exclusive Registrants Only type, which only allows registered servers to perform write operations, achieving access control.

3. **Preempt/Clear**: When a node fails, the surviving node can send the `Reservation Preempt` command to forcibly preempt and clear the failed node's reservation, causing its I/O requests to be rejected by the device, implementing I/O Fencing.


## Hardware and Environment Requirements

|Requirement |SCSI Persistent Reservation |NVMe Reservation |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Storage Device | The storage device should support the SCSI-3 protocol and above, as well as the SPC-3 command set (SCSI Primary Commands - 3) and above, and also support persistent reservation of type 5 (Write Exclusive – Registrants Only). | The storage device is connected via PCIe direct connection or NVMe-oF RDMA.<br/>The storage device supports the NVMe 1.3 protocol (and above) and the NVMe reservation command set. Both the controller and the namespace support the NVMe reservation function, and the Dispersed Namespace is not enabled. |
| Storage Device Configuration | It is recommended to use multipath to bind LUNs and complete the modification of the multipath configuration file on each server. | Each server in YAC/distributed cluster has been configured with a unique ID.<br/> The Dispersed Namespace of the NVMe device cannot be enabled. |
| YCSRA Process | The YCSRA process needs to be online to provide a privileged operation proxy service. Please ensure that the YCSRA process has been started on each server. Otherwise, please refer to the [ycsrootagent tool](../../../Tools Guide/ycsrootagent) to start it manually with sudo. To simplify operation and maintenance, it is recommended to configure the process to start automatically on boot on each server. For specific operations, please refer to [Configure Boot Automatic](../../../Installation and Upgrade/Installation and Deployment/Initial Environment after Installation/Configuring Boot Autostart). | Same as SCSI persistent reservation |

## Configuration and Usage

When deploying YAC/distributed cluster, *yasboot* will automatically select the optimal fence type, and manual configuration is generally not required.

> **Caution**:
>
> The following operations will stop and restart the cluster. Please confirm in advance that the business has stopped or can accept the stop.

1. Confirm that the business has stopped or can tolerate being stopped, and then stop the database cluster.

    ```shell
    $ yasboot cluster stop -c yashandb
    ```

2. Start the yasfs service.

    ```shell
    $ yasfs &
    ```

3. Execute the [ycsctl set_ycr](../../../Tools Guide/ycsctl/User Guide for ycsctl/Cluster Configuration Commands) command to configure the fence type.

    ```shell
    ycsctl set_ycr FENCE_TYPE 2
    ```

4. Stop the yasfs service.

    ```shell
    $ yfscmd exec "shutdown abort"
    ```

5. Start the database cluster.

    ```shell
    $ yasboot cluster start -c yashandb
    ```

6. Check and start the [YCSRA process](../../../Tools Guide/ycsrootagent).

    ```shell
    $ top -c | grep YCSRA

    # If it doesnot exist, you need to start it manually
    $ sudo ycsrootagent start -H /data/yashan/yasdb_data/ycs/ce-1-1&
    YCSRA instance start successfully.
    ```

7. Query the fence status.

    > **Note**:
    >
    > The ycsctl show fence command requires the [YCSRA process](../../../Tools Guide/ycsrootagent) to be online to return results.

    ```shell
    $ ycsctl show fence
    ```

## FAQs

### 1. After enabling reservation-based I/O Fencing, yfscmd fails to add disks

Possible causes and solutions:

- Error Code YAS-00304: The target disk is not a block storage device or does not support the corresponding reservation protocol. Use the fenceResvCheck.sh script for further detection and verification.

- Error Code YAS-00341: The target disk has residual information from registration or reservation by another YAC/distributed cluster. Use the [ycsrootagent scsi clear command](../../../Tools Guide/ycsrootagent) to clean up the residual information after confirming safety (please operate with caution).

### 2. Hardware conditions are all met, but NVMe Reservation still fails to be enabled. What is the reason?

Possible reason: The server ID in the YAC/distributed cluster is not configured or configured incorrectly.

Solution:

1. Check whether the `/etc/nvme/hostid` file exists on each server. If it does not exist, use the `uuidgen` command to create it. For specific operations, please refer to [Configure Installation Environment](../../../Installation and Upgrade/Installation and Deployment/Pre-Installation Preparation/Configuring the Installation Environment).

2. Check whether the IDs on each server are unique.

3. If the server ID has been modified, you need to reconnect the NVMe device.

<span id="fenceResvCheck_usage" name="fenceResvCheck_usage"></span>

## Appendix: Hardware Capability Detection Script

The fenceResvCheck.sh script can automatically detect whether specific storage devices meet the hardware requirements for reservation-based I/O fencing.

- For SCSI devices: Detect whether they support SCSI-3 protocol, SPC-3 command set, and Type 5 Persistent Reservation.

- For NVMe devices: Detect whether the storage device supports NVMe 1.3 protocol, and whether both the Controller and Namespace support the NVMe Reservation function.

### Usage Instructions

The fenceResvCheck script is located in the `$YASDB_HOME/scripts` directory and must be executed under root (or sudo). It supports two modes: single-server detection and dual-server detection.

- Single-Server Detection: Detection is performed only on the server running the script.

- Dual-Server Detection: Detection is performed on both the server running the script and the auxiliary server specified by the -i option, providing more accurate and reliable results.

### Dependencies

- [*ycsrootagent* tool](../../../Tools Guide/ycsrootagent)

- mpathpersist: Required when the server uses DM-Multipath multipath software to manage shared storage

- sshpass: Required when performing dual-server detection without passwordless login configured

### Parameter Description

|Option |Meaning |
| ------------------ | ---------------------------------------------------- |
| *-d* | Disk device name being operated on, multiple device names separated by commas, must be absolute path, cannot be omitted |
| *-i* | Must specify the auxiliary server's IP address for dual-server detection |
| *--port* | SSH connection port for the auxiliary server; default is 22 |
| *-u* | Username for logging into the auxiliary server. This user must have sudo no-password privilege on the auxiliary server |
| *-p* | Password for logging into the auxiliary server. If not specified, it attempts to log in without a password |

> **Note**:
>
> To avoid exposing plaintext passwords, it is recommended to configure no-password login for the user specified by -u.
>
> If there is residual persistent reservation information on the disk specified by -d, this script will automatically clear it, so please ensure that the disk is free before executing the script.

### Usage Example

```shell
$ sudo ./fenceResvCheck.sh -d /dev/yfs/DISK_NAME1 -i 192.168.1.123 -u yashan -p password
```

In the detection results, `Supportive Device` will list the disk paths that meet the hardware requirements, while `Unsupportive Device` will list the disk paths that do not meet these requirements.

If the script detection results show that there are disks that do not meet hardware requirements, please replace them with compatible shared storage devices or consider using other types of I/O Fencing methods.

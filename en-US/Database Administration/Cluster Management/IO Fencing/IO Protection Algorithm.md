The in-flight I/O protection algorithm is a hardware-independent, general-purpose I/O fencing method provided by YCS. If the [shared storage device](../../../Installation and Upgrade/Installation and Deployment/Pre-Installation Preparation/Preparing the Servers.md#Storage) used for deploying the YAC does not meet the requirements for SCSI persistent reservation-based I/O fencing, the in-flight I/O protection algorithm is used by default.

> **Caution**:
>
> The in-flight I/O protection algorithm cannot intercept all in-flight I/O operations. Using this method carries a risk of split-brain. **It is recommended to use** [SCSI IO Fencing](./SCSI IO Fencing).

Compared to hardware-dependent I/O fencing methods, the in-flight I/O protection algorithm may extend the cluster RTO to some extent. When the storage device I/O performance is poor or I/O stalls occur frequently, users need to increase the configuration value of YAC's DISK_HB_KEEP_ALIVE parameter to ensure the algorithm's reliability.

- Type code: 1

- Hardware requirements: None

- Hardware capability detection script: None

- Notes: The DISK_HB_KEEP_ALIVE parameter configuration of YAC must be greater than the maximum theoretical latency of the shared storage device I/O.

## Basic Principles

YAC database instance and the cluster file system will persistently record the in-flight I/O count initiated by this instance to the voting disk according to a certain strategy. If the corresponding server is evicted from the cluster, the cluster database instance will actively block new I/O requests and safely exit after processing the old in-flight I/O.

If the primary server detects that the database instance or cluster file system of the evicted server has in-flight I/O before updating the cluster membership, it will wait for a safe exit or until a timeout occurs.

## Common Issues

#### Self-fencing of Cluster Database

- If the alarm log shows a YcsDbFenced alarm event, it may indicate that YAC database instance blocked I/O requests after the server was evicted. Please refer to the [Alarm Event Description](../../../Reference Manual/Alert Events) for troubleshooting and resolution.
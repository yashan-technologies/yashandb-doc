YashanDB has achieved compatibility certification with multiple server and operating system vendors. For specific details, please refer to the [YashanDB Compatibility List](https://yashandb.com/compatible_certified_partner) published on the official website.

This document will list the  environment-related configurations that we recommend or require when deploying different forms of YashanDB. The operational steps and example commands shown in subsequent manuals are also primarily adapted to these configuration environments. If there are minor differences in actual steps and commands in other compatible environments, it is recommended to refer to the official guidelines of the corresponding operating system or contact the system vendor for resolution. 

>  **Caution**:
>
> If installing YashanDB using a virtual machine, it is essential to set the virtual machine's disk to independent-permanent mode (for example, in VMware, check the mode to be [Independent]-[Permanent] in the virtual machine's [Advanced Disk Settings]; specific configuration methods should be in accordance with each virtual machine platform), otherwise, disk file corruption may occur in scenarios such as power failure.

## Server Configuration

|Item |Recommended Configuration |Minimum Configuration |
| --- | --- | --- |
| Quantity/Usage | * Standalone Deployment: 1 server (single database) or multiple servers (primary-standby) used for deploying database instances<br>* YAC Deployment: 2 or more servers used for deploying cluster instances; when using distributed storage, an additional 3 or more servers are required for mounting NVMe SSDs<br>* Distributed Cluster Deployment: 2 or more servers as compute nodes; 3 or more servers as storage nodes<br>* ISC Distributed ClusterDeployment: 3 or more MN+CN+DN servers | 1 server |
| Operating System | * Redhat Enterprise Linux 7, 8, or 9<br/>* CentOS 7.6 and above<br/>* openEuler 22.03 LTS<br/>* KylinOS V10 (ARM architecture: recommended using V10 SP3 2303 kernel 52.33, Haiguang CPU: recommended using V10 SP3 2303 kernel 52.45) | If large-page memory is to be used, the required Linux kernel version is 2.6 or above<br>If you need to use the NVMe over TCP transport protocol, the Linux kernel version must be 5.0 or higher. |
| CPU         | X86_64, ARM64, or Loongson<br>Hyper-threading, non-hyper-threading<br>Recommended 4C or above; recommended 8C or above in YAC | Number of CPU cores > 2                                     |
| Memory      | Recommended 16G or above; recommended 32G or above in YAC Deployment | >4G                                                         |
| Disk        | Recommended SSD, NVMe SSD required in Distributed Cluster Deployment<br>Recommended storage space 50G or above | >4G                                                         |
| File System | ext4 or xfs                   | Cannot be tmpfs or devtmpfs                                   |
| Network     | Gigabit Ethernet or above, supporting TCP and UDP; Private network for YAC internal communication requires 10GE or above | -         |
| Network Card | Recommended to configure multiple network interfaces for each subnet (such as [public network, private network](./Preparing the Networks), especially private network), and use Linux network bonding technology to provide redundancy for the network interfaces.<br><br/>When deploying a YAC, if SCAN or VIP functionality is required, ensure that all servers in the same cluster use network interface cards with the same name for configuring the public network subnet and support ARP/NDP protocols.<br>If you need to use the NVMe over RDMA transport protocol, the network card type must be RDMA, with the corresponding drivers installed and in normal operating condition. | -        |

<span id="Storage" name="Storage"></span>

## Storage Device Configuration

When deploying a YAC or distributed cluster, in addition to servers, dedicated storage devices are also required. For YAC, either 1 SAN shared storage device or 1 set of NVMe SSDs capable of building NVMe-oF distributed storage is required; for Distributed Cluster, 1 set of NVMe SSDs capable of building NVMe-oF distributed storage is required. 

<span id="Storage" name="Storage"></span>

###  SAN-based Shared Storage

Only YAC Deployment may use SAN shared storage. 

|Item |Recommended Configuration |
| ---- | ------------------------------------------------------------ |
| Hard Disk  | Both SSD and HDD are supported, brands and media should not be mixed            |
| LUN          | 4 or 6 LUNs or more, divided into system disks and data disks: <br/>* System disks: 3 or 5 LUNs, capacity 1G or above (when only 1 LUN is planned for the system disk and its AU Size configuration needs to be 32M, the capacity should be at least 2G)   <br/>* Data disks: 1 or more LUNs, capacity 50G or above |
| Interface    | Storage devices and operating systems support Direct IO, support 512 byte memory alignment and IO size alignment<br>Storage devices support reading and writing 512 byte multiples through `pread64` and `pwrite64` interfaces,<br/>i.e., supports reading and writing the following sizes at once: 512 bytes, 1024 bytes, 1536 bytes...32M |
| Protocol     | If [SCSI Persistent Reservation-based I/O Fencing](../../../Database Administration/Cluster Management/IO Fencing/SCSI IO Fencing) is to be used, SCSI-3 protocol (or above) and SPC-3 command set (SCSI Primary Commands-3, or above) must be supported, and type 5 persistent reservation (Write Exclusive – Registrants Only) must be supported |

> **Caution**:
>
> YAC (when using SAN-based shared storage) supports two methods of [I/O Fencing](../../../Database Administration/Cluster Management/IO Fencing/00IO Fencing), with recommendations to use [SCSI Persistent Reservation-based I/O Fencing](../../../Database Administration/Cluster Management/IO Fencing/SCSI IO Fencing), but this method has certain requirements on the shared storage's capabilities. During the installation process, the [fenceScsiCheck script](../../../Database Administration/Cluster Management/IO Fencing/SCSI IO Fencing.md#fenceScsiCheck_usage) will automatically be invoked to check whether the shared storage and other devices/environments meet the requirements.
>
> If the fenceScsiCheck script detects an error, it will not block the installation, but YAC's I/O Fencing method will be configured as the [In-Transit I/O Protection Algorithm](../../../Database Administration/Cluster Management/IO Fencing/IO Protection Algorithm). If persistent reservation-based I/O Fencing is still required, please make adjustments according to the corresponding error prompts.
>
> If SCSI persistent reservation-based I/O fencing is not used, YAC is at risk of split-brain.

###  NVMe-oF-based Distributed Storage

YAC Deployment may use NVMe-oF distributed storage; Distributed Cluster Deployment requires the use of NVMe-oF distributed storage. 

|Item |Recommended Configuration |
| ---- | ------------------------------------------------------------ |
| Hard Disk  | Both SSD and HDD are supported, brands and media should not be mixed            |
| NVMe-oF Disk      | 4 or 6 NVMe-oF disks or more, divided into system disks and data disks:<br/>* System disks: 3 or 5 namespaces, capacity 1G or above (when only 1 NVMe-oF disk is planned for system disks and its AU Size configuration needs to be 32M, the capacity should be at least 2G)<br/>* Data disks: 1 or more namespaces, capacity 50G or above |
| Transport Type | Supports the following types:<br/>* NVMe over TCP: Requires Linux kernel version 5.0 or above<br/>* NVMe over RDMA: Requires using RDMA network cards and correctly installing the corresponding drivers |

> **Note**:
>
> YAC (when using SAN-based distributed storage)  only support the [IO Protection Algorithm](../../../Database Administration/Cluster Management/IO Fencing/IO Protection Algorithm).
>
> However, during the installation and deployment process, the [fenceScsiCheck script](../../../Database Administration/Cluster Management/IO Fencing/SCSI IO Fencing.md#fenceScsiCheck_usage) will still be automatically invoked to detect whether the hardware requirements for SCSI persistent reservation-based I/O Fencing are met. Detection errors will not block installation and deployment.

<span id="hosts" name="hosts"></span>

## Installation Example Server List

The following lists the server information used in the installation examples in this manual, all servers are X86_64 architecture and have CentOS 7.6 operating system installed. Except for YAC Deployment example which uses different network segments, the example servers in other deployment forms are all located in the same network segment. 

### Standalone Single Database Deployment

In the standalone single database deployment, YashanDB will only be installed on one server, with no standby database.

|Server Name |Server IP |Role |
| -------- | ----------- | ---- |
| host0001    | 192.168.1.2   | Primary   |

### Standalone Primary/Standby Deployment

The standalone primary/standby deployment uses three servers as primary, standby, and standby roles as an example, establishing a high-availability environment with one primary and two standby.

|Server Name |Server IP |Role |
| -------- | ----------- | ---- |
| host0001    | 192.168.1.2   | Primary   |
| host0002    | 192.168.1.3   | Standby   |
| host0003    | 192.168.1.4   | Standby   |

### Standalone Cascading Standby Deployment

The standalone cascading standby deployment uses three servers as primary, standby, and cascading standby roles as an example, establishing a one-primary/one-standby one-cascading standby high-availability environment.

|Server Name |Server IP |Role |
| -------- | ----------- |-----|
| host0001    | 192.168.1.2   | Primary   |
| host0002    | 192.168.1.3   | Standby   |
| host0003    | 192.168.1.4   | Cascading Standby |

### Dual Replication Group Primary/Standby Deployment

The dual replication group primary/standby deployment uses six servers as primary group (deploying 1 primary 2 standby) and standby group (first node as standby, other nodes as cascading standby) as an example, establishing a high-availability environment with dual replication groups.

|Server Name |Server IP |Role |
| -------- | ----------- |-----|
| host0001 (Datacenter A) | 192.168.1.2   | Primary Group: 1-1:1 (Primary)   |
| host0002 (Datacenter A) | 192.168.1.3   | Primary Group: 1-2:2 (Synchronous Standby)   |
| host0003 (Datacenter A) | 192.168.1.4   | Primary Group: 1-3:3 (Synchronous Standby)   |
| host0004 (Datacenter B) | 192.168.10.5  | Standby Group: 2-1:4 (Asynchronous Standby) |
| host0005 (Datacenter B) | 192.168.10.6  | Standby Group: 2-2:5 (Cascade Standby) |
| host0006 (Datacenter B) | 192.168.10.7  | Standby Group: 2-3:6 (Cascade Standby) |

### YAC Deployment

We use two servers + one shared storage as an example for establishing a typical dual instance single database YAC Deployment.

#### Servers

Assume that 2 independent and isolated subnets are planned: one public network and one private network. 

|Server Name | Network |Server IP |Role |
| -------- | ------ | ----- | ----------- |
| host0001 | Public network NIC ens192: 192.168.1.0/24<br/>Private network NIC ens224: 172.16.1.0/24 | ens192: 192.168.1.2<br />ens224: 172.16.1.2 | Database Instance 1 |
| host0002 | Public network NIC ens192: 192.168.1.0/24<br/>Private network NIC ens224: 172.16.1.0/24 | ens192: 192.168.1.2<br />ens224: 172.16.1.2 | Database Instance 2 |
| host0003 | Private network NIC ens224：172.16.1.0/24 | ens224：172.16.1.4 | Storage Server 1 in the distributed storage example    |
| host0004 | Private network NIC ens224：172.16.1.0/24 | ens224：172.16.1.5 | Storage server 2 in the distributed storage example    |

  

**Reserved IP addresses**

- This manual uses 192.168.1.100, 192.168.1.101, and 192.168.1.102 as the reserved SCAN VIPs.
- This manual uses 192.168.1.62 and 192.168.1.63 as the reserved VIPs.



#### Storage Devices

- SAN-based shared storage (4 LUNs have been allocated according to the configuration requirements)

|LUN Name |LUN Path |Disk Usage |
| ------- | ---------------------- | ------ |
| LUN1     | /dev/mapper/lun03-sys0  | System Disk |
| LUN2     | /dev/mapper/lun03-sys1  | System Disk |
| LUN3     | /dev/mapper/lun03-sys2  | System Disk |
| LUN4     | /dev/mapper/lun01-data0  | Data Disk  |

- NVMe-oF distributed storage (already divided into 4 NVMe-oF disks according to configuration requirements)

|NVMe-oF Disk Path |Disk Usage |Host Server |
|--------|--------------------------------------|--------------------------------------|
| /dev/nvme0n1 | System Disk | 192.168.1.4 |
| /dev/nvme0n2 | System Disk | 172.16.1.4 |
| /dev/nvme0n1 | System Disk | 172.16.1.5 |
| /dev/nvme0n3 | Data Disk | 172.16.1.5 |

### Primary/Standby YAC Deployment

The Primary/Standby YAC Deployment uses four servers + two shared storage as an example to build a primary/standby dual instance YAC Deployment.

When using Primary/Standby YAC Deployment, only SAN shared storage is supported.  

#### Servers

Assuming no network isolation planning.

|Server Name | Network |Server IP |Role |
| -------- | ------ | ----- | ----------- |
| host0001 | NIC ens192: 192.168.1.0/24 | 192.168.1.2 | Primary Cluster Database Instance 1 |
| host0002 | NIC ens192: 192.168.1.0/24 | 192.168.1.3 | Primary Cluster Database Instance 2 |
| host0003 | NIC ens192: 192.168.1.0/24 | 192.168.10.4 | Standby Cluster Database Instance 1 |
| host0004 | NIC ens192: 192.168.1.0/24 | 192.168.10.5 | Standby Cluster Database Instance 2 |

  

**Reserved IP addresses**

- This manual uses 192.168.1.100, 192.168.1.101, and 192.168.1.102 as the reserved SCAN VIPs.
- This manual uses 192.168.1.62 and 192.168.1.63 as the reserved VIPs.

   

#### Storage Devices

4 LUNs have been allocated on each shared storage according to the configuration requirements.

|Shared Storage |LUN Name |LUN Path |Disk Usage |
| ------- | ------|---------------- | ------ |
| Shared Storage 1 | LUN1    | /dev/mapper/lun03-sys0  | Primary Cluster System Disk |
| Shared Storage 1 | LUN2    | /dev/mapper/lun03-sys1  | Primary Cluster System Disk |
| Shared Storage 1 | LUN3    | /dev/mapper/lun03-sys2  | Primary Cluster System Disk |
| Shared Storage 1 | LUN4    | /dev/mapper/lun01-data0  | Primary Cluster Data Disk |
| Shared Storage 2 | LUN1'   | /dev/mapper/lun04-sys0  | Standby Cluster System Disk |
| Shared Storage 2 | LUN2'   | /dev/mapper/lun04-sys1  | Standby Cluster System Disk |
| Shared Storage 2 | LUN3'   | /dev/mapper/lun04-sys2  | Standby Cluster System Disk |
| Shared Storage 2 | LUN4'   | /dev/mapper/lun02-data0  | Standby Cluster Data Disk |

### Distributed Cluster Deployment

Distributed  Cluster Deployment uses two CN servers + three DN servers as an example for establishing a typical dual instance single database distributed cluster.

|Server Name |Server IP |Role | NVMe Disks |
| ---------- | ----------- | ------------------------------------- | ------------------------------------- |
| host0001    | 172.16.1.2  | CN 1  | - |
| host0002    | 172.16.1.3  | CN 2            | -           |
| host0003    | 172.16.1.4  | DN 1 | System disks: /dev/nvme0n1, /dev/nvme0n2<br>Data disks: /dev/nvme0n3 |
| host0004    | 172.16.1.5  | DN 2 | System disks: /dev/nvme0n1<br>Data disks: /dev/nvme0n3 |

### ISC Distributed Cluster Deployment

***Example 1***: Use three servers, including 1 MN group with 1 primary and 2 standby nodes, 2 CNs, and 1 DN group with 1 primary and 1 standby node as a sample environment.

|Server Name |Server IP |Role |
| ---------- | ----------- | ------------------------------------- |
| host0001    | 192.168.1.2  | MN: 1-1 (Primary), DN: 3-2 (Standby)  |
| host0002    | 192.168.1.3  | MN: 1-2 (Standby), CN: 2-1            |
| host0003    | 192.168.1.4  | MN: 1-3 (Standby), CN: 2-2, DN: 3-1 (Primary) |

***Example 2***: Use three servers, each including 1 MN group, 2 CNs, and 1 DN group, with 1 primary and 2 standby nodes for both DN and MN as a sample environment.

|Server Name |Server IP |Role |
| -------- | ----------- | ------------------------------------- |
| host0001    | 192.168.1.2  | MN: 1-1 (Primary), DN: 3-2 (Standby)  |
| host0002    | 192.168.1.3  | MN: 1-2 (Standby), CN: 2-1, DN: 3-3 (Standby) |
| host0003    | 192.168.1.4  | MN: 1-3 (Standby), CN: 2-2, DN: 3-1 (Primary) |

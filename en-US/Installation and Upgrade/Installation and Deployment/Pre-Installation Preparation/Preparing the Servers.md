## Server Configuration

The following table lists the recommended and minimum hardware configurations for servers and operating systems required to install YashanDB. Please refer to the [YashanDB Compatibility List](https://yashandb.com/compatible_certified_partner) for all certified compatible servers and operating systems.

> **Note**:
> 
> The YashanDB installation manual outlines general steps and commands for adapting to the environments listed in the table below. If there are slight differences in actual steps, commands, etc., in other compatible environments, it is recommended to refer to the official guides of the corresponding operating systems or contact the system service provider for assistance.

|Item |Recommended Configuration |Minimum Configuration |
| --- | --- | --- |
| Operating System | CentOS 7.6 and above<br/>openEuler 22.03 LTS<br/>KylinOS V10 (ARM architecture: recommended using V10 SP3 2303 kernel 52.33, Haiguang CPU: recommended using V10 SP3 2303 kernel 52.45) | If large-page memory is to be used, the required Linux kernel version is 2.6 or above           |
| CPU         | X86_64, ARM64<br>Hyper-threading, non-hyper-threading<br>Recommended 4C or above; YAC recommended 8C or above | Number of CPU cores > 2                                     |
| Memory      | Recommended 16G or above; YAC recommended 32G or above | >4G                                                         |
| Disk        | SSD recommended<br>Recommended storage space 50G or above | >4G                                                         |
| File System | ext4 or xfs                   | Cannot be tmpfs or devtmpfs                                   |
| Network     | Gigabit Ethernet or above, supporting TCP and UDP; all required to be 10GE within YAC, excluding RDMA | -                                                            |
| Network Card | Recommended to use more than 2 private network cards, using Linux network bonding technology to bond cards for redundancy, configured with one IP address<br>If deploying a YAC and using VIP functionality, ensure all servers in the same cluster configured for VIP have the same card names | -                                                            |

> **Caution**:
>
> If installing YashanDB using a virtual machine, it is essential to set the virtual machine's disk to independent-permanent mode (for example, in VMware, check the mode to be [Independent]-[Permanent] in the virtual machine's [Advanced Disk Settings]; specific configuration methods should be in accordance with each virtual machine platform), otherwise, disk file corruption may occur in scenarios such as power failure.

<span id="Storage" name="Storage" class="yaslink"></span>

## Shared Storage Configuration

|Item |Recommended Configuration |Minimum Configuration |
| ---- | ------------------------------------------------------------ | ------------------------- |
| Disk         | Both SSD and HDD are supported, brands and media should not be mixed            | -                     |
| LUN          | 4 or 6 blocks or more:<br/>3 or 5 system disks (1G or above; if only 1 system disk is used, and AU Size is configured to 32M, then the system disk size must be at least 2G.)<br/> Others are data disks (50G or above) | -                     |
| Interface    | Storage devices and operating systems support Direct IO, support 512 byte memory alignment and IO size alignment<br>Storage devices support reading and writing 512 byte multiples through `pread64` and `pwrite64` interfaces,<br/>i.e., supports reading and writing the following sizes at once: 512 bytes, 1024 bytes, 1536 bytes...32M | -                     |
| Protocol     | If [SCSI Persistent Reservation-based I/O Fencing](../../../YashanDB for Cluster/Yashan Cluster Service/IO Fencing/SCSI IO Fencing) is to be used, SCSI-3 protocol (or above) and SPC-3 command set (SCSI Primary Commands-3, or above) must be supported, and type 5 persistent reservation (Write Exclusive – Registrants Only) must be supported | -                     |

> **Caution**:
>
> YAC supports two methods of [I/O Fencing](../../../YashanDB for Cluster/Yashan Cluster Service/IO Fencing/00IO Fencing), with recommendations to use [SCSI Persistent Reservation-based I/O Fencing](../../../YashanDB for Cluster/Yashan Cluster Service/IO Fencing/SCSI IO Fencing), but this method has certain requirements on the shared storage's capabilities. During the installation process, the [fenceScsiCheck script](../../../YashanDB for Cluster/Yashan Cluster Service/IO Fencing/SCSI IO Fencing.html#fenceScsiCheck_usage) will automatically be invoked to check whether the shared storage and other devices/environments meet the requirements.
>
> If the fenceScsiCheck script detects an error, it will not block the installation, but YAC's I/O Fencing method will be configured as the [In-Transit I/O Protection Algorithm](../../../YashanDB for Cluster/Yashan Cluster Service/IO Fencing/IO Protection Algorithm). If persistent reservation-based I/O Fencing is still required, please make adjustments according to the corresponding error prompts.
>
> If SCSI persistent reservation-based I/O fencing is not used, YAC is at risk of split-brain.

## Deployment Scale

**Standalone Primary/Standby Deployment**

The minimum configuration for Standalone Deployment is 1 server, but it cannot build a high-availability environment.

Multiple servers can be used to build a primary/standby high-availability environment.

**YAC Deployment**

YAC Deployment consists of 2 or more servers + 1 shared storage (optional), establishing a multi-instance database within the cluster for high availability.

For performance considerations, it is recommended to select shared storage in production environments.

Multiple servers can be used to construct a primary/standby high-availability cluster.

**ISC Distributed Cluster Deployment**

The minimum configuration for ISC Distributed Cluster Deployment is 1 server, with MN, CN, and DN deployed on the same server.

At least 3 servers can be used to construct a MN/DN primary/standby configuration and a CN multi-active high-availability environment.

<span id="hosts" name="hosts" class="yaslink"></span>

## Installation Example Server List

The following lists the server information used in the installation examples, all servers are X86_64 architecture and have CentOS 7.6 operating system installed, and the subnet planning does not distinguish between public and private networks.

In production environments, please reasonably plan subnet according to actual needs, it is recommended to allocate mutually independent and isolated public and private subnets.

### Standalone Single Database Deployment

In the standalone single database deployment, YashanDB will only be installed on one server, with no standby database.

|Server Name |Server IP |Role |
| -------- | ----------- | ---- |
| Server 1    | 192.168.1.2   | Primary   |

### Standalone Primary/Standby Deployment

The standalone primary/standby deployment uses three servers as primary, standby, and standby roles as an example, establishing a high-availability environment with one primary and two standby.

|Server Name |Server IP |Role |
| -------- | ----------- | ---- |
| Server 1    | 192.168.1.2   | Primary   |
| Server 2    | 192.168.1.3   | Standby   |
| Server 3    | 192.168.1.4   | Standby   |

### Standalone Cascade Backup Deployment

The standalone cascade backup deployment uses three servers as primary, standby, and cascade backup roles as an example, establishing a one-primary/one-standby one-cascade backup high-availability environment.

|Server Name |Server IP |Role |
| -------- | ----------- |-----|
| Server 1    | 192.168.1.2   | Primary   |
| Server 2    | 192.168.1.3   | Standby   |
| Server 3    | 192.168.1.4   | Cascade Backup |

### Dual Replication Group Primary/Standby Deployment

The dual replication group primary/standby deployment uses six servers as primary group (deploying 1 primary 2 standby) and backup group (first node as standby, other nodes as cascade backup) as an example, establishing a high-availability environment with dual replication groups.

|Server Name |Server IP |Role |
| -------- | ----------- |-----|
| Server 1 (Datacenter A) | 192.168.1.2   | Primary Group: 1-1:1 (Primary)   |
| Server 2 (Datacenter A) | 192.168.1.3   | Primary Group: 1-2:2 (Synchronous Standby)   |
| Server 3 (Datacenter A) | 192.168.1.4   | Primary Group: 1-3:3 (Synchronous Standby)   |
| Server 4 (Datacenter B) | 192.168.10.5  | Backup Group: 2-1:4 (Asynchronous Standby) |
| Server 5 (Datacenter B) | 192.168.10.6  | Backup Group: 2-2:5 (Cascade Backup) |
| Server 6 (Datacenter B) | 192.168.10.7  | Backup Group: 2-3:6 (Cascade Backup) |

### YAC Deployment

YAC Deployment uses two servers + one shared storage as an example for establishing a typical dual instance single database YAC Deployment.

Servers:

|Server Name |Server IP |Server VIP |Role |
| -------- | ----------- | ----------- | ----------- |
| Server 1    | 192.168.1.2   | 192.168.1.62 | Database Instance 1 |
| Server 2    | 192.168.1.3   | 192.168.1.63 | Database Instance 2 |

[VIP](../../../YashanDB for Cluster/Yashan Cluster Service/VIP) is an optional extended functionality in YAC Deployment, and if used, it should follow these rules:


- All nodes in the same YAC must adopt a unified VIP configuration strategy (the VIP configuration strategy between primary/standby clusters can be different):

    - Nodes either uniformly configure VIP or do not configure it.

    - VIPs uniformly use either IPv4 or IPv6.

- VIPs should be planned as reserved free IP addresses, which do not exist or cannot be pinged before YAC deployment is completed.

- VIP must be in the same subnet, the same network card as the instance listening address (LISTEN_ADDR), and have the same port number.

- The names of the network cards used for configuring VIP on all servers in the same cluster must be the same.

Shared storage: (Four LUNs have been allocated according to the configuration requirements and mounted through multipathing)

|LUN Name |LUN Path |Role |
| ------- | ---------------------- | ------ |
| LUN1     | /dev/mapper/lun03-sys0  | System Disk |
| LUN2     | /dev/mapper/lun03-sys1  | System Disk |
| LUN3     | /dev/mapper/lun03-sys2  | System Disk |
| LUN4     | /dev/mapper/lun01-data0  | Data Disk  |

<span id="vip_planning" name="vip_planning" class="yaslink"></span>

### Primary/Standby Cluster Deployment

The primary/standby cluster deployment uses four servers + two shared storage as an example to build a primary/standby dual instance YAC Deployment.

Servers:

|Server Name |Server IP |Server VIP |Role |
| -------- | ----------- | ----------- | ----------- |
| Server 1    | 192.168.1.2   | 192.168.1.62 | Primary Cluster Database Instance 1 |
| Server 2    | 192.168.1.3   | 192.168.1.63 | Primary Cluster Database Instance 2 |
| Server 3    | 192.168.10.4  | 192.168.10.64 | Backup Cluster Database Instance 1 |
| Server 4    | 192.168.10.5  | 192.168.10.65 | Backup Cluster Database Instance 2 |

[VIP](../../../YashanDB for Cluster/Yashan Cluster Service/VIP) is an optional extended functionality in YAC Deployment, and if used, it should follow these rules:


- All nodes in the same YAC must adopt a unified VIP configuration strategy (the VIP configuration strategy between primary/standby clusters can be different):

    - Nodes either uniformly configure VIP or do not configure it.

    - VIPs uniformly use either IPv4 or IPv6.

- VIPs should be planned as reserved free IP addresses, which do not exist or cannot be pinged before YAC deployment is completed.

- VIP must be in the same subnet, the same network card as the instance listening address (LISTEN_ADDR), and have the same port number.

- The names of the network cards used for configuring VIP on all servers in the same cluster must be the same.

Shared storage: (Eight LUNs have been allocated according to the configuration requirements and mounted through multipathing)

|Shared Storage |LUN Name |LUN Path |Role |
| ------- | ------|---------------- | ------ |
| Shared Storage 1 | LUN1    | /dev/mapper/lun03-sys0  | Primary Cluster System Disk 1 |
| Shared Storage 1 | LUN2    | /dev/mapper/lun03-sys1  | Primary Cluster System Disk 2 |
| Shared Storage 1 | LUN3    | /dev/mapper/lun03-sys2  | Primary Cluster System Disk 3 |
| Shared Storage 1 | LUN4    | /dev/mapper/lun01-data0  | Primary Cluster Data Disk |
| Shared Storage 2 | LUN1'   | /dev/mapper/lun04-sys0  | Backup Cluster System Disk 1 |
| Shared Storage 2 | LUN2'   | /dev/mapper/lun04-sys1  | Backup Cluster System Disk 2 |
| Shared Storage 2 | LUN3'   | /dev/mapper/lun04-sys2  | Backup Cluster System Disk 3 |
| Shared Storage 2 | LUN4'   | /dev/mapper/lun02-data0  | Backup Cluster Data Disk |

### ISC Distributed Cluster Deployment

***Example 1***: ISC Distributed Cluster Deployment uses three servers, including 1 MN group with 1 primary and 2 standby nodes, 2 CNs, and 1 DN group with 1 primary and 1 standby node as a sample environment.

|Server Name |Server IP |Role |
| ---------- | ----------- | ------------------------------------- |
| Server 1    | 192.168.1.2  | MN: 1-1 (Primary), DN: 3-2 (Standby)  |
| Server 2    | 192.168.1.3  | MN: 1-2 (Standby), CN: 2-1            |
| Server 3    | 192.168.1.4  | MN: 1-3 (Standby), CN: 2-2, DN: 3-1 (Primary) |

***Example 2***: ISC Distributed Cluster Deployment uses three servers, each including 1 MN group, 2 CNs, and 1 DN group, with 1 primary and 2 standby nodes for both DN and MN as a sample environment.

|Server Name |Server IP |Role |
| -------- | ----------- | ------------------------------------- |
| Server 1    | 192.168.1.2  | MN: 1-1 (Primary), DN: 3-2 (Standby)  |
| Server 2    | 192.168.1.3  | MN: 1-2 (Standby), CN: 2-1, DN: 3-3 (Standby) |
| Server 3    | 192.168.1.4  | MN: 1-3 (Standby), CN: 2-2, DN: 3-1 (Primary) |

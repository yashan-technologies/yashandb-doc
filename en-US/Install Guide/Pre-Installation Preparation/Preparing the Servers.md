## Server Configuration

The following table lists the recommended and minimum hardware configurations for servers and operating systems required to install YashanDB. Please refer to the [YashanDB Compatibility List](https://yashandb.com/compatible_certified_partner) for all certified compatible servers and operating systems.

> **Note**:
> 
> The YashanDB installation manual outlines general steps and commands for adapting to the environments listed in the table below. If there are slight differences in actual steps, commands, etc., in other compatible environments, it is recommended to refer to the official guides of the corresponding operating systems or contact the system service provider for assistance.

|Item |Recommended Configuration |Minimum Configuration |
| --- | --- | --- |
| Operating System | Redhat Enterprise Linux 7, 8, or 9<br/>CentOS 7.6 and above<br/>openEuler 22.03 LTS<br/>KylinOS V10 (ARM architecture: recommended using V10 SP3 2303 kernel 52.33, Haiguang CPU: recommended using V10 SP3 2303 kernel 52.45) | If large-page memory is to be used, the required Linux kernel version is 2.6 or above<br>If NVMe oF protocal based TCP is to be used, the required Linux kernel version is 5.0 or above |
| CPU         | X86_64, ARM64, or Loongson<br>Hyper-threading, non-hyper-threading<br>Recommended 4C or above; YAC recommended 8C or above | Number of CPU cores > 2                                     |
| Memory      | Recommended 16G or above; YAC recommended 32G or above | >4G                                                         |
| Disk        | SSD recommended, NVMe SSD required in Distributed Cluster<br>Recommended storage space 50G or above | >4G                                                         |
| File System | ext4 or xfs                   | Cannot be tmpfs or devtmpfs                                   |
| Network     | Gigabit Ethernet or above, supporting TCP and UDP; all required to be 10GE within YAC, excluding RDMA | -         |
| Network Card | Recommended to configure multiple network interfaces for each subnet (such as [public network, private network](../../All Manuals/Installation and Upgrade/Installation and Deployment/Pre-Installation Preparation/典型组网), especially private network), and use Linux network bonding technology to provide redundancy for the network interfaces.<br>When deploying a YAC, if SCAN or VIP functionality is required, ensure that all servers in the same cluster use network interface cards with the same name for configuring the public network subnet and support ARP/NDP protocols.<br>When deploying a distributed cluster, the storage network must use RDMA network interface cards with corresponding drivers installed and in normal operating condition. | -        |

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
| Protocol     | If [SCSI Persistent Reservation-based I/O Fencing](../../All Manuals/YashanDB for Cluster/Yashan Cluster Service/IO Fencing/SCSI IO Fencing) is to be used, SCSI-3 protocol (or above) and SPC-3 command set (SCSI Primary Commands-3, or above) must be supported, and type 5 persistent reservation (Write Exclusive – Registrants Only) must be supported | -                     |

> **Caution**:
>
> YAC supports two methods of [I/O Fencing](../../All Manuals/共享集群/集群服务管理/IO Fencing/00IO Fencing), with recommendations to use [SCSI Persistent Reservation-based I/O Fencing](../../All Manuals/共享集群/集群服务管理/IO Fencing/基于SCSI持久预留的IO Fencing), but this method has certain requirements on the shared storage's capabilities. During the installation process, the [fenceScsiCheck script](../../All Manuals/共享集群/集群服务管理/IO Fencing/基于SCSI持久预留的IO Fencing.html#fenceScsiCheck_usage) will automatically be invoked to check whether the shared storage and other devices/environments meet the requirements.
>
> If the fenceScsiCheck script detects an error, it will not block the installation, but YAC's I/O Fencing method will be configured as the [In-Transit I/O Protection Algorithm](../../All Manuals/共享集群/集群服务管理/IO Fencing/在途IO保护算法). If persistent reservation-based I/O Fencing is still required, please make adjustments according to the corresponding error prompts.
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

**Distributed Cluster Deployment**

Distributed Cluster Deployment consists of more than 2 CN servers + 3 DN servers, establishing a cluster database including multi-computing instances and an intelligent distributed storage network for high availability.

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

YAC Deployment uses two servers + one shared storage as an example for establishing a typical dual instance single database YAC Deployment.

Server: (assuming planning for two independent and isolated subnets: public network and private network)

|Server Name | Network |Server IP |Role |
| -------- | ------ | ----- | ----------- |
| host0001 | Public network NIC ens192: 192.168.1.0/24<br/>Private network NIC ens224: 172.16.1.0/24 | ens192: 192.168.1.2<br />ens224: 172.16.1.2 | Database Instance 1 |
| host0002 | Public network NIC ens192: 192.168.1.0/24<br/>Private network NIC ens224: 172.16.1.0/24 | ens192: 192.168.1.2<br />ens224: 172.16.1.2 | Database Instance 2 |



Reserved IP addresses:

- In version 23.4.4.100 and above, if SCAN functionality is required, the subnet corresponding to the public network interface card must also reserve 1-3 IP addresses from the subnet corresponding to the public network interface card for each cluster as SCAN VIPs. This chapter uses 192.168.1.100, 192.168.1.101, and 192.168.1.102 as examples.

    [SCAN](../../All Manuals/共享集群/集群服务管理/SCAN管理) is an optional extended functionality in YAC Deployment, and if used, it should follow these rules:

    

    - The server's network interface must be an Ethernet card and support ARP/NDP protocols.

    - The public network information for YAC must be configured first (either by specifying the `--public-network` parameter in the `yasboot package ce gen` command during installation, or by executing the `ycsctl add network` command after installation completion). This configuration is a prerequisite for SCAN functionality activation.

    - 1-3 SCAN VIP addresses need to be planned for a cluster, and IPv4 or IPv6 should be used consistently.

    - After configuring SCAN, database drivers or clients connecting through the SCAN domain name should also be version 23.4.4.100 or above.



    - On the DNS server, configure DNS resolution rules according to the planned SCAN domain name (e.g., scan.example.com) and the reserved SCAN VIPs, for example:

        ```bash
        # DNS records
        scan.example.com IN A 192.168.1.100
        ……
        
        # reverse lookup records
        100.1.168.192.in-addr.arpa IN PTR scan.example.com
        ……
        ```

- In version 23.4.2.100 and above, if VIP functionality is required, the subnet corresponding to the public network interface card must also reserve 1 IP address per server as VIP. This chapter uses 192.168.1.62 and 192.168.1.63 as examples.

    [VIP](../../All Manuals/共享集群/集群服务管理/VIP管理) is an optional extended functionality in YAC Deployment, and if used, it should follow these rules:
    
    

    - The server's network interface must be an Ethernet card and support ARP/NDP protocols.

    - The public network information for YAC must be configured first (either by specifying the `--public-network` parameter in the `yasboot package ce gen` command during installation, or by executing the `ycsctl add network` command after installation completion). This configuration is a prerequisite for VIP functionality activation.

    - All nodes in the same YAC must adopt a unified VIP configuration strategy (the VIP configuration strategy between primary/standby clusters can be different):

    - Nodes either uniformly configure VIP or do not configure it.

    - VIPs uniformly use either IPv4 or IPv6.

    - VIP addresses should be planned as reserved idle IP addresses.

    - VIP must belong to the public network subnet and must be in the same subnet as the instance listening address (LISTEN_ADDR).



Shared storage: (Four LUNs have been allocated according to the configuration requirements)

|LUN Name |LUN Path |Role |
| ------- | ---------------------- | ------ |
| LUN1     | /dev/mapper/lun03-sys0  | System Disk |
| LUN2     | /dev/mapper/lun03-sys1  | System Disk |
| LUN3     | /dev/mapper/lun03-sys2  | System Disk |
| LUN4     | /dev/mapper/lun01-data0  | Data Disk  |

<span id="vip_planning" name="vip_planning" class="yaslink"></span>

### Primary/Standby Cluster Deployment

The primary/standby cluster deployment uses four servers + two shared storage as an example to build a primary/standby dual instance YAC Deployment.

Server: (assuming no network isolation planning)

|Server Name | Network |Server IP |Role |
| -------- | ------ | ----- | ----------- |
| host0001 | NIC ens192: 192.168.1.0/24 | 192.168.1.2 | Primary Cluster Database Instance 1 |
| host0002 | NIC ens192: 192.168.1.0/24 | 192.168.1.3 | Primary Cluster Database Instance 2 |
| host0003 | NIC ens192: 192.168.1.0/24 | 192.168.10.4 | Standby Cluster Database Instance 1 |
| host0004 | NIC ens192: 192.168.1.0/24 | 192.168.10.5 | Standby Cluster Database Instance 2 |



Reserved IP addresses:

- In version 23.4.4.100 and above, if SCAN functionality is required, the subnet corresponding to the public network interface card must also reserve 1-3 IP addresses from the subnet corresponding to the public network interface card for each cluster as SCAN VIPs. This chapter uses 192.168.1.100, 192.168.1.101, and 192.168.1.102 as examples.

    [SCAN](../../All Manuals/共享集群/集群服务管理/SCAN管理) is an optional extended functionality in YAC Deployment, and if used, it should follow these rules:

    

    - The server's network interface must be an Ethernet card and support ARP/NDP protocols.

    - The public network information for YAC must be configured first (either by specifying the `--public-network` parameter in the `yasboot package ce gen` command during installation, or by executing the `ycsctl add network` command after installation completion). This configuration is a prerequisite for SCAN functionality activation.

    - 1-3 SCAN VIP addresses need to be planned for a cluster, and IPv4 or IPv6 should be used consistently.

    - After configuring SCAN, database drivers or clients connecting through the SCAN domain name should also be version 23.4.4.100 or above.



    - On the DNS server, configure DNS resolution rules according to the planned SCAN domain name (e.g., scan.example.com) and the reserved SCAN VIPs, for example:

        ```bash
        # DNS records
        scan.example.com IN A 192.168.1.100
        ……
        
        # reverse lookup records
        100.1.168.192.in-addr.arpa IN PTR scan.example.com
        ……
        ```

- In version 23.4.2.100 and above, if VIP functionality is required, the subnet corresponding to the public network interface card must also reserve 1 IP address per server as VIP. This chapter uses 192.168.1.62 and 192.168.1.63 as examples.

    [VIP](../../All Manuals/共享集群/集群服务管理/VIP管理) is an optional extended functionality in YAC Deployment, and if used, it should follow these rules:
        


    - The server's network interface must be an Ethernet card and support ARP/NDP protocols.

    - The public network information for YAC must be configured first (either by specifying the `--public-network` parameter in the `yasboot package ce gen` command during installation, or by executing the `ycsctl add network` command after installation completion). This configuration is a prerequisite for VIP functionality activation.

    - All nodes in the same YAC must adopt a unified VIP configuration strategy (the VIP configuration strategy between primary/standby clusters can be different):

    - Nodes either uniformly configure VIP or do not configure it.

    - VIPs uniformly use either IPv4 or IPv6.

    - VIP addresses should be planned as reserved idle IP addresses.

    - VIP must belong to the public network subnet and must be in the same subnet as the instance listening address (LISTEN_ADDR).



Shared storage: (4 LUNs have been allocated on each shared storage according to the configuration requirements)

|Shared Storage |LUN Name |LUN Path |Role |
| ------- | ------|---------------- | ------ |
| Shared Storage 1 | LUN1    | /dev/mapper/lun03-sys0  | Primary Cluster System Disk 1 |
| Shared Storage 1 | LUN2    | /dev/mapper/lun03-sys1  | Primary Cluster System Disk 2 |
| Shared Storage 1 | LUN3    | /dev/mapper/lun03-sys2  | Primary Cluster System Disk 3 |
| Shared Storage 1 | LUN4    | /dev/mapper/lun01-data0  | Primary Cluster Data Disk |
| Shared Storage 2 | LUN1'   | /dev/mapper/lun04-sys0  | Standby Cluster System Disk 1 |
| Shared Storage 2 | LUN2'   | /dev/mapper/lun04-sys1  | Standby Cluster System Disk 2 |
| Shared Storage 2 | LUN3'   | /dev/mapper/lun04-sys2  | Standby Cluster System Disk 3 |
| Shared Storage 2 | LUN4'   | /dev/mapper/lun02-data0  | Standby Cluster Data Disk |

### Distributed Cluster Deployment

Distributed  Cluster Deployment uses two CN servers + three DN servers as an example for establishing a typical dual instance single database distributed cluster.

Servers:

|Server Name |Server IP |Role |
| ---------- | ----------- | ------------------------------------- |
| host0001    | 192.168.1.2  | CN 1  |
| host0002    | 192.168.1.3  | CN 2            |
| host0003    | 192.168.1.4  | DN 1 |
| host0004    | 192.168.1.5  | DN 2 |
| host0005    | 192.168.1.6  | DN 3 |

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

Network preparation refers to planning and configuring the subnets, IP addresses, ports, and other network resources for the database cluster to be built.

## Network Planning 

Based on the communication attributes required by YashanDB servers, we define the following two logical subnets: 

- Public Network: Mainly used for external business access to YashanDB, DBA for database management, and database tools for database command invocation, etc.
- Private Network: Mainly used for internal communication of YashanDB.

Enterprises can place the above public and private networks into the same physical subnet, or put them into different physical subnets according to actual needs. 

To ensure the security of application data and isolate illegal commands from the internet, it is recommended to divide the networking of YashanDB based on functionality into independent and isolated networks.

::: tabs

== Standalone Deployment

The typical networking of Standalone Deployment is shown in the figure below. This network scheme uses one primary and two standby examples, and it is recommended that the primary database and each standby database be deployed on different servers.

![](./image/alone.png)

|Address |Description |Suggested Subnet |
|--------------------|-------------|-----------------|
| Database external listening address | Used to connect to the database, providing external database services | Public Network        |
| Primary-standby database communication address | Used for internal communication between primary and standby databases, inaccessible to database users <br/> Only required for primary/standby deployment, this address needs to be planned | Private Network    |

== YAC Deployment

 Based on different storage devices, the YAC have the following two typical networking solutions: 

-  Networking Solution One: This networking solution takes as an example a dual-instance single-database YAC environment built with 2 servers + 1 shared storage device, where instances should be deployed on different servers.  Where the storage network is a dedicated network for shared storage, such as SAN or NAS. 
- Networking Solution Two: This networking solution takes as an example a dual-instance single-database YAC environment built with 2 servers + multiple servers forming an NVMe-oF distributed storage, where instances should be deployed on different servers. 

![](./image/cluster.png)

|Address |Description |Suggested Subnet |
|--------------------|-------------|-----------------|
| Database external listening address | Used for connecting to the database and providing external database services<br>Including three types of addresses: LISTEN_ADDR, SCAN VIP, and VIP. SCAN VIP and VIP must be in the same subnet and on the same network interface as LISTEN_ADDR. | Public Network         |
| Internal yasdb listening address<br>Internal YCS listening address | Used respectively for communication between database instances within the cluster and internal communication between YCS instances within the cluster. Database users cannot access these addresses. <br>These two types of addresses on the same server can use the same IP address with different port numbers. | Private Network |
| Internal NVMe-oF communication address | Used for internal NVMe-oF communication when using distributed storage. Database users cannot access this address. | Private Network    |
| Primary-standby clusters communication address | Used for internal communication between primary and standby clusters, inaccessible to database users <br/> Only required for primary/standby clusters deployment, this address needs to be planned | Private Network |

== Distributed Cluster Deployment

The typical networking of Distributed Cluster Deployment is shown in the figure below. This network scheme uses 5 servers (three are mounted with NVMe storage) to construct a 2CN+3DN distributed cluster as an example. The nodes should be deployed on different servers.

![](./image/ssc.png)

|Address                                             |Description                                         |Suggested Subnet |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ----------------------------- |
| Database external listening address                          | used to connect to the database, providing external database services | Public Network                |
| Internal yasdb listening address<br/>Internal YCS listening address | Used respectively for communication between database instances within the cluster and internal communication between YCS instances within the cluster. Database users cannot access these addresses. <br/>These two types of addresses on the same server can use the same IP address with different port numbers. | Private Network               |
| Internal NVMe-oF communication address                       | Used for internal NVMe-oF communication. Database users cannot access this address. | Private Network               |

== ISC Distributed Cluster Deployment

The typical networking of ISC Distributed Cluster Deployment is shown in the figure below. This network scheme uses 1 MN group, 2 CNs, and 1 DN group (both DN group and MN group are configured with 1 primary and 2 standby) as an example. It is recommended that the primary/standby nodes of each group be deployed on different servers.

![](./image/dstb.png)  

|Address |Description |Suggested Subnet |
|--------------------|-------------|-----------------|
| Database external listening address | The listening address of the CN node used to connect to the database, providing external database services | Public Network         |
| REPLICATION_ADDR (required for primary/standby deployment in MN/DN groups)<br/>DIN_ADDR | REPLICATION_ADDR is used for communication between primary/standby nodes within the same group, DIN_ADDR is used for internal communication between MN, CN, and DN nodes across groups, inaccessible to database users <br/> Each node group has different port numbers, and these two types of addresses on the same server can use the same IP | Private Network    |

:::

##  IP Planning 

Based on the actual network segmentation, each server in the above networking requires one or more IP addresses to be planned for it. 

 For YAC Deployment, IP address reservation is also required: 

- It is recommended to reserve 1-3 IP addresses as SCAN VIPs in the public network for each cluster, to meet potential future SCAN functionality requirements.

- It is recommended to reserve 1 IP address per server in the public network as a VIP to meet potential future VIP functionality requirements. 

##  DNS Resolution Configuration 

Enterprises can choose whether to configure DNS resolution for IP addresses in the public network based on actual needs. 

If the [SCAN](../../../Database Administration/Cluster Management/SCAN Management) function may be used subsequently, then DNS resolution rules must be configured in the DNS server according to the planned SCAN domain name (for example, scan.example.com) and the reserved SCAN VIP. The following is a configuration example: 

```bash
# DNS records
scan.example.com IN A 192.168.1.100
……

# reverse lookup records
100.1.168.192.in-addr.arpa IN PTR scan.example.com
……
```

<span id="openports" name="openports" class="yaslink"></span>
## Port Planning

Running YashanDB requires the use of a series of ports (the functions and related information of these ports are listed in the [Port List](../../../Reference Manual/Port List)). YashanDB has established a set of port allocation rules and provides default port values that need to be specified during the installation process. Users may modify the port numbers according to their own network planning, but should adhere to the port allocation rules to avoid port conflicts.

The port allocation rules and default values are as follows:

- The database listening port is specified by the `beging-port`/start port parameter in the installation steps, with a default value of 1688.

- The database listening port will serve as the initial value for calculating other internal communication ports.

  - If the SQL mode is specified as [mysql mode](../../../Product Overview/Compatibility/Compatibility with MySQL), a set of listening ports for mysql protocol usage should also be planned, with a default value of 1690 (Standalone (primary/standby) deployment) or 1691(YAC deployment) and each standby database defaulting to +3, and so on.
  - In YAC Deployment, the VIP listening port is the same as the database listening (LISTEN_ADDR) port by default, and no additional port numbers need to be allocated.
  -  In YAC /Distributed Cluster Deployment, all NVMe listening ports are numbered sequentially. 
  - In ISC Distributed Cluster Deployment, port numbers are generated in the order of MN, CN, DN, while internal communication ports are generated in the order of inter-group communication and intra-group communication. When deploying multiple DN groups, each DN group defaults to +3, and so on.

  |Deployment Type       |Database Listening                            |Internal Communication                          |yasom                       |yasagent                 |
  | ---------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------- | ------------------------------------- |
  | Standalone Deployment              | yashan mode: 1688 <br/> mysql mode: 1688 and 1690, where 1690 is the default listening port for mysql protocol | replication link (required for primary/standby deployment): initial value +1, defaulting to 1689 | initial value -13, defaulting to 1675 | initial value -12, defaulting to 1676 |
  | YAC Deployment                     | yashan mode: 1688 <br/> mysql mode: 1688 and 1691, where 1691 is the default listening port for mysql protocol | * Communication between database instances within the same cluster: initial value +1, defaulting to 1689<br/>* Communication between YCS instances: initial value +100, defaulting to 1788<br/>* replication link (required for primary/standby deployment): initial value +2, defaulting to 1690<br/>* NVMe-oF Communication: initial value of first datadisk +2, defaulting to 1700, while other datadisks incremented by +1; default value of first systemdisk 1700+datadisks quantity, other systemdisks incremented by +1 | initial value -13, defaulting to 1675 | initial value -12, defaulting to 1676 |
  | Distributed Cluster Deployment     | 1688                                                         | * Communication between database instances within the same cluster: initial value +1, defaulting to 1689<br/>* Communication between YCS instances: initial value +100, defaulting to 1788<br/>* NVMe-oF Communication: initial value of first datadisk +2, defaulting to 1700, while other datadisks incremented by +1; default value of first systemdisk 1700+datadisks quantity, other systemdisks incremented by +1 | initial value -13, defaulting to 1675 | initial value -12, defaulting to 1676 |
  | ISC Distributed Cluster Deployment | MN: initial value -10, defaulting to 1678<br/>CN: 1688<br/>DN: initial value +10, defaulting to 1698 | Corresponding listening ports incremented by +1, generated in the order of inter-group communication and intra-group communication<br/><br/>* Inter-group communication: MN is 1679, CN is 1689, DN is 1699<br/>* Intra-group communication: MN is 1680, CN is 1690, DN is 1700 | initial value -13, defaulting to 1675 | initial value -12, defaulting to 1676 |

- If a server requires visual deployment web services, port 9001 should also be used.

## Open Ports

- **Method 1: Disable Firewall**

   If you plan to use the method of disabling the firewall to open all ports on the server, you can directly execute the following command on all servers:

   ```shell
   ## Disable firewall
   # systemctl stop firewalld 
   ## Disable auto-start
   # systemctl disable firewalld
   ```

- **Method 2: Add to Whitelist**

   If the firewall cannot be closed, ports can be opened by adding whitelist rules. Please first calculate the port numbers according to the rules listed in the port planning, then follow the instructions below to open all database listening ports and yasom ports on all servers.

   1. Check the open ports of the firewall:

      ```shell
      # firewall-cmd --zone=public --list-ports
      ```

   2. Add the port to the firewall:

      The following example demonstrates how to add port 1688 to the firewall; the method is the same for other ports.

      ```shell
      ## Add (--permanent means it will take effect permanently; without this parameter, it will be invalid after a restart)
      # firewall-cmd --zone=public --add-port=1688/tcp --permanent
      ## Reload
      # firewall-cmd --reload
      ## Check
      # firewall-cmd --zone=public --query-port=1688/tcp
      ```

      > **Note**:
      >
      > Opening ports by adding to the whitelist may still result in deployment failure due to communication issues during the installation process. In such cases, contact the company's network administrator to clarify the reasons and re-open the ports before proceeding with installation.

   3. To remove a port that has been added to the whitelist, use the following command:

      ```shell
      # firewall-cmd --zone=public --remove-port=1688/tcp --permanent
      ```

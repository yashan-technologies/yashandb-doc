## Functionality Introduction

Single Client Access Name (SCAN) is an intelligent connection management mechanism that provides a unified access entry for YAC. Through virtual IP technology and smart DNS resolution, it enables clients to access backend cluster services via a single logical name (i.e., SCAN domain name) and dynamically distributes connection requests through load balancing algorithms. Users do not need to be aware of specific cluster node topology changes, as both node failures and scaling operations are transparent to users, simplifying client configuration while maximizing service high availability.

In YAC Deployment, SCAN is a cluster service resource managed by YCS, and its workflow is roughly as follows:

1. Plan the SCAN and 1-3 SCAN VIPs, and configure DNS resolution rules in the DNS server according to the planning information.

2. When adding SCAN to the cluster, YCS retrieves the SCAN VIPs from the DNS server and manages them.

    - When the cluster starts, SCAN VIPs are assigned to the cluster nodes. Nodes running SCAN VIPs will start a SCAN listener to handle connection requests and dynamically distribute connections to database instances according to load balancing rules.

    - SCAN VIPs and their listeners default to running on different nodes, unless the number of nodes and SCAN VIPs are inconsistent, in which case some nodes may run 0 or multiple SCAN VIPs and their listeners.

    - When a node fails, the SCAN VIPs and their listeners running on it will automatically migrate to healthy nodes.

3. Client/drivers initiate database connection requests through SCAN. After DNS resolution, all SCAN VIP addresses are obtained and one SCAN VIP is randomly connected. When the SCAN listener receives a SCAN connection request, it returns the database instance connection address (If the [VIP](./VIP Management) is enabled, return the VIP:port; otherwise, return the LISTEN_ADDR.) with the lowest load to the client/driver based on real-time load information. The client/driver then formally establishes a connection with the database instance according to the

The start and stop of the SCAN and high availability management require the YCSRA process to be online to provide privilege operation proxy services. Please ensure that the YCSRA process is started on each server; otherwise, refer to the [ycsrootagent tool](../../Tools Guide/ycsrootagent) to manually start it with sudo. To simplify operations and management, it is recommended to configure this process to start automatically on boot on each server. For specific operations, please refer to [Configuring Boot Autostart](../../Installation and Upgrade/Installation and Deployment/Initial Environment after Installation/Configuring Boot Autostart).

> **Note**: 
>
> If you need to restart the network on the cluster server running SCAN, please manually execute `ycsctl stop scan` to stop the SCAN currently running on that node. Once the network restart is complete, execute `ycsctl start scan` to restart the SCAN.


## Configuration Requirements



- The server's network interface must be an Ethernet card and support ARP/NDP protocols.

- The public network information for YAC must be configured first (either by specifying the `--public-network` parameter in the `yasboot package ce gen` command during installation, or by executing the `ycsctl add network` command after installation completion). This configuration is a prerequisite for SCAN functionality activation.

- 1-3 SCAN VIP addresses need to be planned for a cluster, and IPv4 or IPv6 should be used consistently.

- After configuring SCAN, database drivers or clients connecting through the SCAN domain name should also be version 23.4.4.100 or above.



<span id="scan_configuration" name="scan_configuration"></span>

## Configure SCAN for an Existing YAC

If the user did not configure SCAN during the installation and deployment of YAC, or if upgrading from an old version cluster that does not support (or has not enabled) SCAN to a new version cluster that supports SCAN, you can follow the process below to configure SCAN resources for the cluster.

### Prerequisites

- The current database version is 23.4.4.100 or above, or has been upgraded to that version or above. 

- SCAN IP addresses that meet the configuration requirements have been planned and DNS configuration completed. The configuration format is as follows:

    ```bash
    # DNS records
    scan.example.com IN A 192.168.1.100
    ……
    
    # reverse lookup records
    100.1.168.192.in-addr.arpa IN PTR scan.example.com
    ……
    ```



###  ## Step 1: Configure Public Network Subnet

1. Log in to the database installation server using the installation user.


2. View cluster information to confirm whether the public network and service ports have been configured.

    ```shell
    # View cluster YCR configuration information
    $ ycsctl show config
        Cluster name: yashandb, config version: 4
        ……
        
        Default resource yasfs: enabled
        Resource SCAN: disabled
        Resource vip: disabled
        ……

        Nodes in cluster:
        Node name: yas1, yascs/yasfs inter connect URL: 172.16.1.2:1788, Node ID: 1
            public service port: 1688
            yasdb instance name:yasdb-1-1, yasdb instance id:1
        Node name: yas2, yascs/yasfs inter connect URL: 172.16.1.3:1788, Node ID: 2
            yasdb instance name:yasdb-1-2, yasdb instance id:1
    ```
    The absence of Network information in the echo indicates that the public network has not been configured yet, and the absence of public service port under node yas2 indicates that the service port number has not been configured for this node yet.

3. (Optional) If the public network has not been configured, perform the following operations:

    ```shell
    $ ycsctl add network -subnet 192.168.1.0/24/ens192
    ```



###  Step 2: Configure and Start SCAN

1. Add SCAN configuration information.

    ```shell
    $ ycsctl add scan -scanname scan.example.com -p 1688
    ```
    At this point, YCS will obtain the SCAN VIP addresses from the DNS resolution rules configured on the DNS server.

2. Start all SCAN VIPs.

    ```shell
    $ ycsctl start scan
    ```

3. Check SCAN configuration.

    ```shell
    $ ycsctl show config
        Cluster name: yashandb, config version: 6
        ……

        Network: 192.168.1.0/24
        Resource SCAN: enabled
        SCAN name: scan.example.com, listening port: 1688
        SCAN VIP: 192.168.1.100, ordinal number: 1
        SCAN VIP: 192.168.1.101, ordinal number: 2
        SCAN VIP: 192.168.1.102, ordinal number: 3
        Resource vip: enabled
        ……
        
        Nodes in cluster:
        Node name: yas1, yascs/yasfs inter connect URL: 172.16.1.2:1788, Node ID: 1
            public service port: 1688
            yasdb instance name:yasdb-1-1, yasdb instance id:1
            VIP: 192.168.1.62/24/ens192, home node: yas1
        Node name: yas2, yascs/yasfs inter connect URL: 172.16.1.3:1788, Node ID: 2
            public service port: 1688
            yasdb instance name:yasdb-1-2, yasdb instance id:1
            VIP: 192.168.1.63/24/ens192, home node: yas2
    ```

4. View the current topology status of the cluster.

    ```shell
    $ ycsctl status
    ---------------------------------------------------------------------------------------------
    Self Host ID|Cluster Master ID|YasFS Master ID|YasDB Master ID|Active Host Count
    ---------------------------------------------------------------------------------------------
    1            1                 1               1               2
    ---------------------------------------------------------------------------------------------
    Host ID   |Target    |State     |YasFS     |YasDB     |VIP
    ---------------------------------------------------------------------------------------------
    1          online     online     online     online     host1.online
    2          online     online     online     online     host2.online
    ---------------------------------------------------------------------------------------------
    SCAN VIP 1: host2.online   SCAN VIP 2: host1.online   SCAN VIP 3: host1.online
    ```

## Modify SCAN Resource Configuration

When the user performs cluster environment operations and maintenance (such as modifying network configurations, reassigning IP addresses, and ports), they can follow the process below to update the configuration information of VIP resources.

> **Caution**:
>
> Before adjusting the corresponding configuration, please ensure that upper-layer business temporarily does not depend on connection information based on SCAN.

###  Change SCAN Configuration

To update the SCAN domain name or port, you need to first delete the old SCAN resource and then configure the new SCAN resource.

 

1. Log in to the database installation server using the installation user.


2. Stop all SCAN VIPs.

    ```shell
    $ ycsctl stop scan
    ```

3. Delete SCAN configuration.

    ```shell
    $ ycsctl remove scan
    ```


4. To update the SCAN VIP address or SCAN domain name, the DNS resolution rules need to be updated on the DNS server.

5. Add new SCAN configuration information.

    ```shell
    $ ycsctl add scan -scanname scan_new.example.com -p 1688
    ```

6. Start all SCAN VIPs again.

    ```shell
    $ ycsctl start scan
    ```

7. View the cluster configuration to confirm the modification results.

    ```shell
    $ ycsctl show config
        Cluster name: yashandb, config version: 6
        ……

        Network: 192.168.1.0/24
        Resource SCAN: enabled
        SCAN name: scan_new.example.com, listening port: 1688
        SCAN VIP: 192.168.1.100, ordinal number: 1
        SCAN VIP: 192.168.1.101, ordinal number: 2
        SCAN VIP: 192.168.1.102, ordinal number: 3
        Resource vip: enabled
        ……
        
        Nodes in cluster:
        Node name: yas1, yascs/yasfs inter connect URL: 172.16.1.2:1788, Node ID: 1
            public service port: 1688
            yasdb instance name:yasdb-1-1, yasdb instance id:1
            VIP: 192.168.1.62/24/ens192, home node: yas1
        Node name: yas2, yascs/yasfs inter connect URL: 172.16.1.3:1788, Node ID: 2
            public service port: 1688
            yasdb instance name:yasdb-1-2, yasdb instance id:1
            VIP: 192.168.1.63/24/ens192, home node: yas2
    ```

8. View the current topology status of the cluster.

    ```shell
    $ ycsctl status
    ---------------------------------------------------------------------------------------------
    Self Host ID|Cluster Master ID|YasFS Master ID|YasDB Master ID|Active Host Count
    ---------------------------------------------------------------------------------------------
    1            1                 1               1               2
    ---------------------------------------------------------------------------------------------
    Host ID   |Target    |State     |YasFS     |YasDB     |VIP
    ---------------------------------------------------------------------------------------------
    1          online     online     online     online     host1.online
    2          online     online     online     online     host2.online
    ---------------------------------------------------------------------------------------------
    SCAN VIP 1: host2.online   SCAN VIP 2: host1.online   SCAN VIP 3: host1.online
    ```

###  Relocate SCAN VIP Manually

SCAN VIPs have automatic relocation capability, and usually does not require maintenance of the correspondence between SCAN VIPs and database instance nodes.

Common automatic relocation scenarios are as follows:

- When the node running a SCAN VIP fails, it will automatically relocate to other healthy nodes.

- If the number of SCAN VIPs is greater than the number of database instance nodes, the extra SCAN VIPs will be automatically migrated to new instance nodes during cluster instance scale-out.

The steps for manual migration are as follows:

1. Log in to the database installation server using the installation user.


2. Query the sequence number of the SCAN VIP (i.e., the ordinal number field).

    ```shell
    $ ycsctl show config
        Cluster name: yashandb, config version: 6
        ……

        Network: 192.168.1.0/24
        Resource SCAN: enabled
        SCAN name: scan.example.com, listening port: 1688
        SCAN VIP: 192.168.1.100, ordinal number: 1
        SCAN VIP: 192.168.1.101, ordinal number: 2
        SCAN VIP: 192.168.1.102, ordinal number: 3
        Resource vip: enabled
        ……
        
        Nodes in cluster:
        Node name: yas1, yascs/yasfs inter connect URL: 172.16.1.2:1788, Node ID: 1
            public service port: 1688
            yasdb instance name:yasdb-1-1, yasdb instance id:1
            VIP: 192.168.1.62/24/ens192, home node: yas1
        Node name: yas2, yascs/yasfs inter connect URL: 172.16.1.3:1788, Node ID: 2
            public service port: 1688
            yasdb instance name:yasdb-1-2, yasdb instance id:1
            VIP: 192.168.1.63/24/ens192, home node: yas2
    ```

3. Manually relocate the target SCAN VIP.
    
    ```shell
    # relocate to specified node
    $ ycsctl relocate scan -scannumber 3 -node yas2

    # Automatically relocate
    $ ycsctl relocate scan -scannumber 3
    ```

### Delete SCAN Resource Configuration

 

1. Log in to the database installation server using the installation user.


2. Stop all SCAN VIPs.

    ```shell
    $ ycsctl stop scan
    ```

3. Delete SCAN configuration.

    ```shell
    $ ycsctl remove scan
    ```
 


 

###  Delete Public Network Configuratio

Before deleting public network configuration, all VIP and SCAN configurations must be deleted first.


1. Log in to the database installation server using the installation user.


2. Delete public network configuration.

    ```shell
    $ ycsctl remove network
    ```

 

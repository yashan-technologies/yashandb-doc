## Functionality Introduction

VIP (Virtual IP) is a node service resource managed by YCS. The database instances listen to the VIP and provide services. The difference between VIP and the instance listening address (LISTEN_ADDR) is that VIP can migrate between a failed instance and a normal instance, making instance-level failures transparent to users.

When a cluster node fails, YCS first performs a voting arbitration to evict the failed node from the cluster. It then redistributes the VIP resources of the failed node to the normal nodes based on high availability and load balancing principles. After the normal node takes over the failed VIP, it will restart the VIP on the server running on that node and redirect requests and traffic connected to that VIP to the current server at the network layer. Meanwhile, the database instance managed by that node will dynamically add listening capability for the VIP. Through the above failover process, the VIP of the failed instance quickly restores connection availability, and the cluster failure is made as transparent as possible to the customer's business.

The start and stop of the VIP and high availability management require the YCSRA process to be online to provide privilege operation proxy services. Please ensure that the YCSRA process is started on each server; otherwise, refer to the [ycsrootagent tool](../../Tools Guide/ycsrootagent) to manually start it with sudo. To simplify operations and management, it is recommended to configure this process to start automatically on boot on each server. For specific operations, please refer to [Configuring Boot Autostart](../../Installation and Upgrade/Installation and Deployment/Initial Environment after Installation/Configuring Boot Autostart).

> **Note**: 
>
> If you need to restart the network on the cluster server running VIP, please manually execute `ycsctl stop vip` to stop the VIP currently running on that node. Once the network restart is complete, execute `ycsctl start vip` to restart the VIP.

## Configuration Requirements



- The server's network interface must be an Ethernet card and support ARP/NDP protocols.

- The public network information for YAC must be configured first (either by specifying the `--public-network` parameter in the `yasboot package ce gen` command during installation, or by executing the `ycsctl add network` command after installation completion). This configuration is a prerequisite for VIP functionality activation.

- All nodes in the same YAC must adopt a unified VIP configuration strategy (the VIP configuration strategy between primary/standby clusters can be different):

    - Nodes either uniformly configure VIP or do not configure it.

    - VIPs uniformly use either IPv4 or IPv6.

- VIP addresses should be planned as reserved idle IP addresses.

- VIP must belong to the public network subnet and must be in the same subnet as the instance listening address (LISTEN_ADDR).



<span id="vip_configuration" name="vip_configuration"></span>

## Configure VIP for an Existing YAC

If the user did not configure VIP during the installation and deployment of YAC, or if upgrading from an old version cluster that does not support (or has not enabled) VIP to a new version cluster that supports VIP, you can follow the process below to configure VIP resources for the cluster.

### Prerequisites

- The current database version is 23.4.2.100 or above, or has been upgraded to that version or above.

- IP addresses that meet VIP configuration requirements have been planned.



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



### Step 2 (Optional): Configure Node Service Port

If a node's service port number is not configured or needs to modify it, perform the following operations.

 

1. Stop the cluster

    ```shell
    $ yasboot cluster stop -c yashandb
    ```

2. Start the YASFS service.

    ```shell
    $ yasfs -D /data/yashan/yasdb_data/ycs/ce-1-1&
    ```

3. Configure service port number for the target node, please fill in the actual node names and port numbers in the command.

    ```shell
    $ ycsctl modify node yas2 serviceport=1688
    ```

4. Stop the YASFS service.

    ```shell
    $ yfscmd exec "shudown abort"
    ```

5. Start the cluster.

    ```shell
    $ yasboot cluster start -c yashandb
    ```


### Step 3: Configure and Start VIP

1. Add VIP configuration information.

    ```shell
    # Fill in the actual node names, VIP addresses, and subnet masks in the command
    $ ycsctl add vip -n host0001 --vip 192.168.1.62/24
    $ ycsctl add vip -n host0002 --vip 192.168.1.63/24
    ```

2. Log in to each server in the cluster to start VIP resources one by one.

    ```shell
    $ ycsctl start vip
    ```

3. Check the status of VIP resources.

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

    $ ycsctl show config
        Cluster name: yashandb, config version: 6
        ……

        Network: 192.168.1.0/24
        Resource SCAN: disabled
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

##  Modify VIP Resource Configuration

When the user performs cluster environment operations and maintenance, such as modifying network configurations, reassigning IP addresses, and ports, they can follow the process below to update the configuration information of VIP resources.

> **Caution**:
>
> Before adjusting the corresponding configuration, please ensure that upper-layer business no longer relies on connection information based on the target VIP and port, or that the cluster has other online VIP resources that can provide connection services.

###  Change the Node Service Port

To change the node service port, please perform the following operations:

 

1. Stop the cluster

    ```shell
    $ yasboot cluster stop -c yashandb
    ```

2. Start the YASFS service.

    ```shell
    $ yasfs -D /data/yashan/yasdb_data/ycs/ce-1-1&
    ```

3. Configure service port number for the target node, please fill in the actual node names and port numbers in the command.

    ```shell
    $ ycsctl modify node yas2 serviceport=1688
    ```

4. Stop the YASFS service.

    ```shell
    $ yfscmd exec "shudown abort"
    ```

5. Start the cluster.

    ```shell
    $ yasboot cluster start -c yashandb
    ```


###  Change VIP Address

To change the VIP address, you need to first delete the old VIP and then configure the new VIP address.

1. Log in to the database installation server using the installation user.


2. Delete the old VIP of the target node.

    ```shell
    # This example will force stop the VIP of node yas1 and delete its configuration information

    $ ycsctl remove vip -n yas1 -f
    ```

3. Add new VIP resource configuration information and start VIP resources.

    ```shell
    # Example assigns the address 192.168.1.71 to node yas1 as the new VIP address
    $ ycsctl add vip -n yas1 192.168.1.71/24/ens192

    $ ycsctl start vip
    ```

3. View the cluster configuration to confirm the modification results.

    ```shell
    $ ycsctl show config
        Cluster name: yashandb, config version: 10
        ……

        Network: 192.168.1.0/24
        Resource SCAN: disabled
        Resource vip: enabled
        ……

        Nodes in cluster:
        Node name: yas1, yascs/yasfs inter connect URL: 172.16.1.2:1788, Node ID: 1
            public service port: 1688
            yasdb instance name:yasdb-1-1, yasdb instance id:1
            VIP: 192.168.1.71/24/ens192, home node: yas1
        ……
    ```

###  Delete All VIP Resource Configuration

When deleting VIP resource configurations, only one node's configuration can be deleted at a time.

> **Warn**:
>
> Deleting a VIP is a **dangerous operation**. Please ensure that the database instance on the node where the VIP resides is in an idle state with no business running before deleting the VIP.

1. Log in to the database installation server using the installation user.


2. Delete VIP resource configurations for each node one by one.

    ```shell
    $ ycsctl remove vip -n yas1 -f
    $ ycsctl remove vip -n yas2 -f
    ```

 

###  Delete Public Network Configuratio

Before deleting public network configuration, all VIP and SCAN configurations must be deleted first.


1. Log in to the database installation server using the installation user.


2. Delete public network configuration.

    ```shell
    $ ycsctl remove network
    ```



## Common Issues

###  How to handle the "specified ip is reachable" prompt when adding a VIP?

This prompt indicates that the IP address assigned to the VIP is already occupied by another server. You should plan and reserve VIP addresses as required.

###  How to handle the "specified ip is reachable" prompt when adding a VIP?

This prompt indicates that the network interface associated with the VIP has an abnormal status or unsupported protocol. Please confirm whether the network interface status is normal, and whether the network interface is an Ethernet card and supports ARP/NDP protocols

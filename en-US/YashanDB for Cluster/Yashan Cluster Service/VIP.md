## Functionality Introduction

VIP (Virtual IP) is a node service resource managed by YCS. The database instances listen to the VIP and provide services. The difference between VIP and the instance listening address (LISTEN_ADDR) is that VIP can migrate between a failed instance and a normal instance, making instance-level failures transparent to users.

When a cluster node fails, YCS first performs a voting arbitration to evict the failed node from the cluster. It then redistributes the VIP resources of the failed node to the normal nodes based on high availability and load balancing principles. After the normal node takes over the failed VIP, it will restart the VIP on the server running on that node and redirect requests and traffic connected to that VIP to the current server at the network layer. Meanwhile, the database instance managed by that node will dynamically add listening capability for the VIP. Through the above failover process, the VIP of the failed instance quickly restores connection availability, and the cluster failure is made as transparent as possible to the customer's business.

The start and stop of the VIP and high availability management require the YCSRA process to be online to provide privilege operation proxy services. Please ensure that the YCSRA process is started on each server; otherwise, refer to the [ycsrootagent tool](../../Tools Guide/ycsrootagent) to manually start it with sudo. To simplify operations and management, it is recommended to configure this process to start automatically on boot on each server. For specific operations, please refer to [Configuring Boot Autostart](../../Installation and Upgrade/Installation and Deployment/Initial Environment after Installation/Configuring Boot Autostart).

> **Note**: 
>
> If you need to restart the network on the cluster server running VIP, please manually execute `ycsctl stop vip` to stop the VIP currently running on that node. Once the network restart is complete, execute `ycsctl start vip` to restart the VIP.

## Configuration Requirements


- All nodes in the same YAC must adopt a unified VIP configuration strategy (the VIP configuration strategy between primary/standby clusters can be different):

    - Nodes either uniformly configure VIP or do not configure it.

    - VIPs uniformly use either IPv4 or IPv6.

- VIPs should be planned as reserved free IP addresses, which do not exist or cannot be pinged before YAC deployment is completed.

- VIP must be in the same subnet, the same network card as the instance listening address (LISTEN_ADDR), and have the same port number.

- The names of the network cards used for configuring VIP on all servers in the same cluster must be the same.

<span id="vip_configuration" name="vip_configuration" class="yaslink"></span>

## Enable VIP

If the user did not configure VIP during the installation and deployment of YAC, or if upgrading from an old version cluster that does not support (or has not enabled) VIP to a new version cluster that supports VIP, you can follow the process below to configure VIP resources for the cluster.

### Prerequisites

- The current database version is 23.4.2.100 or above, or has been upgraded to that version or above.

- IP addresses that meet VIP configuration requirements have been planned.

<span id="serviceport_configuration" name="serviceport_configuration" class="yaslink"></span>

### Step 1: Configure Node Service Port Number

1. Log in to the database installation server as the installation user.

2. Check and confirm that the listening port number (LISTEN_ADDR) of each database instance is consistent with the corresponding cluster node service port number (service port).
```shell
# View the LISTEN_ADDR listening port configured by the database instance
$ yasboot cluster config show -c yashandb -q LISTEN_ADDR
+---------------------------+
| node   | LISTEN_ADDR      |
+---------------------------+
| ce-1-1 | 192.168.1.2:1688 |
+--------+------------------+
| ce-1-2 | 192.168.1.3:1688 |
+--------+------------------+

# View cluster YCR configuration information
$ ycsctl show config
    Cluster name: yashandb, config version: 6
    Cluster id: 3eedfe29261c713ea258a22f181c5218
    Network timeout: 30s
    Disk heartbeat keep alive: 60s
    Fence type: SCSI I/O Fencing
    Default resource yasfs: enabled
    Resource vip: disabled
    Shell in cluster:
    Start shell:   start.sh
    Stop shell:    stop.sh
    Monitor shell: monitor.sh
    Nodes in cluster:
    Node name: yas1, yascs/yasfs inter connect URL: 192.168.7.133:1788, Node ID: 1
                                            # The node yas1 has not configured the service port number
        yasdb instance name:yasdb-1-1, yasdb instance id:1
    Node name: yas2, yascs/yasfs inter connect URL: 192.168.7.134:1788, Node ID: 2
        public service port: 1601           # The node yas2 has configured the service port number as 1601, but it is inconsistent with the LISTEN_ADDR listening port 1688
        yasdb instance name:yasdb-1-2, yasdb instance id:1
```

3. If the port configuration does not meet the configuration rules, perform the following operations.
```shell
# Stop the cluster
$ yasboot cluster stop -c yashandb

# Start the YASFS service
$ yasfs -D /data/yashan/yasdb_data/ycs/ce-1-1&

# Configure service port number for each node, please fill in the actual node names and port numbers in the command
$ ycsctl modify node yas1 serviceport=1688
$ ycsctl modify node yas2 serviceport=1688

# Stop the YASFS service
$ yfscmd exec "shudown abort"

# Start the cluster
$ yasboot cluster start -c yashandb
```

### Step 2: Configure and Start VIP

1. Add VIP configuration information.
```shell
# Fill in the actual node names, VIP addresses, subnet masks, and network card names in the command
$ ycsctl add vip -n yas1 192.168.1.62/24/ens192
$ ycsctl add vip -n yas2 192.168.1.63/24/ens192
```

2. Log in to each server in the cluster to start VIP resources one by one.
```shell
$ ycsctl start vip
```

3. Check the status of VIP resources
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
```

## Modify VIP

When the user performs cluster environment operations and maintenance (such as modifying network configurations, reassigning IP addresses, and ports), they can follow the process below to update the configuration information of VIP resources.

### Step 1: Delete Old VIP Resource Configuration

Before deleting the old VIP resource, please ensure that the upper-level business no longer relies on the connection service provided by this VIP, or that there are other online VIP resources in the cluster that can provide connection services.

```shell
# This example will force stop the VIP of node yas1 and delete its configuration information
$ ycsctl remove vip -n yas1 -f
```

### Step 2: Configure and Start New VIP Resources

1. Confirm whether the port number listened by the VIP needs to change. If so, perform the following steps:
```shell
# Stop the cluster
$ yasboot cluster stop -c yashandb

# Start the YASFS service
$ yasfs -D /data/yashan/yasdb_data/ycs/ce-1-1&

# Modify the service port number of node yas1, please fill in the actual node names and port numbers in the command
$ ycsctl modify node yas1 serviceport=1689

# Stop the YASFS service
$ yfscmd exec "shudown abort"

# Start the cluster
$ yasboot cluster start -c yashandb
```

2. Add new VIP resource configuration information and start VIP resources
```shell
# Example assigns the address 192.168.1.121 to node yas1 as the new VIP address
$ ycsctl add vip -n yas1 192.168.1.121/24/ens192

$ ycsctl start vip

# View cluster topology
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
```

## Common Issues

### Adding VIP Failed

- Error Code YAS-00432: The status of the network card associated with the VIP is abnormal, or the protocol is not supported. Please confirm whether the network card status is normal and whether it is an Ethernet card that supports ARP/NDP protocols.

- Error Code YAS-05767: The VIP configuration information is invalid (e.g., format error, invalid IP address, invalid subnet mask, etc.).

- Error Message "specified ip is reachable": The IP address assigned to the VIP has already been occupied by another server.

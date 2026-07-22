### ycsctl add network

This command is used to add public network configuration to the cluster.  VIP or SCAN VIP functionality cannot be used until the public network is configured.

The command format and parameter descriptions are as follows:

```shell
ycsctl add network -subnet <subnet/netmask[/interface]> [-offline]
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
| -subnet | subnet/netmask[/interface] | Specifies the public network configuration information, in the format: `subnet/subnet mask/network interface name`, where the network interface name is optional.<br />* If no network interface name is specified, any network interface that can access the given subnet will be used. <br />* If a network interface name is specified, ensure that all servers in the same cluster have a network interface with the same name that can access the given subnet. |
| -offline | | Optional parameter. Indicates that this command can be executed even when YCS is not running. |

### ycsctl modify network

This command is used to modify YCS public network subnet configuration.

Before executing this command, ensure that SCAN and VIP are not configured for the current cluster.

The command format and parameter descriptions are as follows:

```shell
ycsctl modify network -subnet <subnet/netmask[/interface]> [-offline]
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
| -subnet | subnet/netmask[/interface] | Specifies the public network configuration information, in the format: `subnet/subnet mask/network interface name`, where the network interface name is optional.<br />* If no network interface name is specified, any network interface that can access the given subnet will be used. <br />* If a network interface name is specified, ensure that all servers in the same cluster have a network interface with the same name that can access the given subnet. |
| -offline | | Optional parameter. Indicates that this command can be executed even when YCS is not running. |

***Example***

```shell
# Modify public network subnet online
$ ycsctl modify network -subnet 192.168.2.0/24/ens192
```

### ycsctl remove network

This command is used to delete the public network configuration for the cluster. All VIP and SCAN configurations must be deleted before the public network can be deleted.

The command format is as follows:

```shell
ycsctl remove network
```

### ycsctl add vip

This command is used to dynamically add a VIP resource during the cluster operation. 

The command format and parameter descriptions are as follows:

```shell
ycsctl add vip -n <nodename> --vip <ip/netmask[/interface]> [-offline]
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
| -n | nodename | Specifies the name of the instance node to which the VIP resource should be added. |
| --vip | ip/netmask[/interface] | Specifies the VIP resource configuration information, which must belong to a public subnet. The format is: `IP address/subnet mask/network interface name`, where the network interface name is optional.<br />* If no network interface name is specified, the network interface name configuration from the network will be inherited. If it is not possible to ensure that all servers in the same cluster have consistent public network interface names, the network interface name must be omitted.<br />* If a network interface name is specified, ensure it does not conflict with the network specification, and that all servers in the same cluster have a network interface with the same name that can access the public network subnet. |
| -offline | | Optional parameter. Indicates that this command can be executed even when YCS is not running. |

### ycsctl start vip

This command is used to start VIP resources on the current YCS.  

The command format and parameter descriptions are as follows:

```shell
ycsctl start vip [-n <nodename>]
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
| -n | nodename | Optional parameter. Specifies the name of the instance node for which the VIP resource should be started. If omitted, the VIP resource of the current node will be started by default. |

### ycsctl stop vip

This command is used to stop the acquired VIP resources on the current YCS.  

The command format and parameter descriptions are as follows:

```shell
ycsctl stop vip [-n <nodename>]
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
| -n | nodename | Optional parameter. Specifies the name of the instance node for which the VIP resource should be stopped. If omitted, the VIP resource of the current node will be stopped by default. |

### ycsctl relocate vip

This command is used to relocate specified VIP resources to other YCS nodes.

The command format and parameter descriptions are as follows:

```shell
ycsctl relocate vip -n <nodename> [-t <target_nodename>]
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
| -n | nodename | Specifies the name of the instance node to which the target VIP resource belongs. The mapping between instance nodes and VIPs can be viewed via `ycsctl show config`. |
| -t | target_nodename | Optional parameter. Specifies the name of the target node to which the VIP resource will be relocated. If omitted, YCS will automatically relocate the VIP resource according to high availability and round-robin principles. |

### ycsctl modify vip

This command is used to modify the configuration of VIP resources in the cluster.

After executing this command, the VIP resources of the current cluster will be automatically stopped. You need to execute the ycsctl start vip command to start the resources before you can normally access the database through VIP.

The command format and parameter descriptions are as follows:

```shell
ycsctl modify vip -n <nodename> --vip <ip/netmask[/interface]> [-offline]
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
| -n | nodename | Specifies the name of the instance node to which the VIP resource should be modified. |
| --vip | ip/netmask[/interface] | Specifies the new VIP resource configuration information, which must belong to a public subnet. The format is: `IP address/subnet mask/network interface name`, where the network interface name is optional.<br />* If no network interface name is specified, the network interface name configuration from the network will be inherited. If it is not possible to ensure that all servers in the same cluster have consistent public network interface names, the network interface name must be omitted.<br />* If a network interface name is specified, ensure it does not conflict with the network specification, and that all servers in the same cluster have a network interface with the same name that can access the public network subnet. |
| -offline |  | Optional parameter. Indicates offline execution. If not specified, online execution is used by default. |

***Example***

```shell
# Modify VIP online
$ ycsctl modify vip -n host0001 --vip 192.168.1.70/24/ens192
```

### ycsctl remove vip

This command is used to dynamically remove a VIP resource during the cluster operation. 

The command format and parameter descriptions are as follows:

```shell
ycsctl remove vip -n <nodename> [-f]
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
| -n | nodename | Specifies the name of the instance node where the target VIP resource resides. This name can be queried via the `ycsctl show config` command, where the value of the `home node` field for the target VIP resource represents this name. |
| -f | | Optional parameter. Indicates to forcibly stop the target VIP resource to be removed. |

### ycsctl add scan

This command is used to add a SCAN resource to the cluster. 

The command format and parameter descriptions are as follows:

```shell
ycsctl add scan -scanname <scan_name> -p <port> [-offline]
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
| -scanname | scan_name | Specifies the SCAN domain name, which must be a fully qualified hostname. <br />Clients/drivers can connect to the database via this name. |
| -p | port | Specifies the listening port for SCAN. |
| -offline | | Optional parameter. Indicates that this command can be executed even when YCS is not running. |

### ycsctl start scan

This command is used to start SCAN VIPs. 

The command format and parameter descriptions are as follows:

```shell
ycsctl start scan [-scannumber <ordinal_number>] [-node <nodename>]
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
| -scannumber | ordinal_number | Optional parameter. Specifies the ordinal number of the target SCAN VIP to be started, with allowed values of 1, 2, or 3. If omitted, it indicates starting all SCAN VIPs. <br />The mapping between ordinal numbers and SCAN VIPs can be viewed via `ycsctl show config`. |
| -node | nodename | Optional parameter. Specifies the name of the instance node for the target SCAN VIP to be started. If omitted, YCS will automatically assign the starting node in a round-robin fashion. |

### ycsctl stop scan

This command is used to stop SCAN VIPs. 

The command format and parameter descriptions are as follows:

```shell
ycsctl stop scan [-scannumber <ordinal_number>]
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
| -scannumber | ordinal_number | Optional parameter. Specifies the ordinal number of the target SCAN VIP to be stopped, with allowed values of 1, 2, or 3. If omitted, it indicates starting all SCAN VIPs. <br />The mapping between ordinal numbers and SCAN VIPs can be viewed via `ycsctl show config`. |

### ycsctl relocate scan

This command is used to manually relocate a SCAN VIP (i.e., manually move a specific SCAN VIP to another node). 

The command format and parameter descriptions are as follows:

```shell
ycsctl relocate scan -scannumber <ordinal_number> [-node <nodename>]
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
| -scannumber | ordinal_number | Optional parameter. Specifies the ordinal number of the target SCAN VIP to be relocated, with allowed values of 1, 2, or 3. If omitted, it indicates starting all SCAN VIPs. <br />The mapping between ordinal numbers and SCAN VIPs can be viewed via `ycsctl show config`. |
| -node | nodename | Optional parameter. Specifies the name of the target node to which the target SCAN VIP will be relocated. If omitted, YCS will automatically relocate the SCAN VIP according to high availability and round-robin principles. |

### ycsctl modify scan

This command is used to modify the SCAN domain name or port of the cluster.

If you need to modify the domain name, before executing this command, you need to configure DNS resolution rules on the DNS server according to the new domain name plan.

After executing this command, the SCAN resources of the current cluster will be automatically stopped. You need to execute ycsctl start scan to start the resources before you can normally use the domain name to connect to the database.

The command format and parameter descriptions are as follows:

```shell
ycsctl modify scan -scanname <scan_name> -p <port> [-offline]
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
| -scanname |scan_name| If you need to modify the domain name, you need to specify a new SCAN domain name; otherwise, you need to specify the original domain name. |
| -p | port | If you need to modify the port, you need to specify a new SCAN listening port; otherwise, you need to specify the original port. |
| -offline | | Optional parameter, indicating that the command can be executed when YCS is not started. |

***Example***

```shell
# Modify the SCAN domain name online
$ ycsctl modify scan -scanname scan.new_example.com -p 1688
```

### ycsctl remove scan

This command is used to remove a SCAN. 

The command format and parameter descriptions are as follows:

```shell
ycsctl remove scan [-f]
```

| Parameter Option   | Description          |
| ---------- | ------------------------------------------------------------ |
| -f | Optional parameter. Indicates forced termination and deletion of the SCAN. |

This article will introduce the OCI connection configuration methods for YashanDB.

## Using URL Connection

Establish a connection using the URL of the target database. Single IP, multiple IPs, or multiple IP groups are supported. URL format is as follows:

* Single IP: `host:port[/pdb_name]`.

* Multiple IPs: `serverType:host:port,host:port,host:port,host:port[/pdb_name]`, multiple addresses are separated by `,`, connections will poll to corresponding nodes based on serverType configuration.

* Multiple IP groups: `serverType:host:port,host:port;host:port,host:port[/pdb_name]`, multiple IP groups are separated by `;`, when connecting, it will first poll the corresponding nodes within the group according to the serverType configuration. If all connections within the group fail, it will access the next group in order of priority (the higher the position, the higher the priority).

Parameter meanings:

* host:port[/pdb_name]: The listening address of the server. To connect to YashanDB deployed as a CDB, you must use the OCI driver of version v23.5.1.100 or later and specify the correct [target container name](../../../Database Administration/​​Multitenant Management/​​Managing PDBs​​/​View PDBs); if omitted, the connection will default to the CDB root.

* serverType: Connection mode, optional options include [primary&#124;standby&#124;loadBalance&#124;primaryLoadBalance&#124;standbyLoadBalance].


## Using Alias Connection

Establish a connection using an alias. You need to define an alias pointing to the target database in $YASDB_HOME/client/yasc_service.ini.

```shell
vi yasc_service.ini

CITEST = 192.168.1.2:1688
REMOTE = PRIMARY:192.168.1.2:1688,192.168.1.3:1688,192.168.1.4:1688
REMOTE2 = STANDBY:192.168.1.2:1688,192.168.1.3:1688,192.168.1.4:1688
ABROAD = LOADBALANCE:192.168.1.2:1688,192.168.1.3:1688,192.168.1.4:1688
ABROAD2 = PRIMARYLOADBALANCE:192.168.1.2:1688,192.168.1.3:1688,192.168.1.4:1688
ABROAD3 = STANDBYLOADBALANCE:192.168.1.2:1688,192.168.1.3:1688,192.168.1.4:1688
```

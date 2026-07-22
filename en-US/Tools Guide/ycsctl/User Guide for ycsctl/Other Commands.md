### ycsctl show fence

This command is used to check whether all servers in the cluster have privilege read and write access to the data disk, requiring the YCSRA process to be online to provide query service. 

The command format is as follows:

```shell
ycsctl show fence
```

***Example***

```shell
$ ycsctl show fence
Fence Type: SCSI I/O Fencing
----------------------

Host ID   |Permission
0          ALLOW
1          BAN
2          ALLOW
3          ALLOW
```

The following describes each configuration item:

| Field Name       |Meaning                  |
| ------------ | -------------------------------- |
| Fence Type | The I/O fencing type used by the current cluster. |
| Host ID | Server ID. |
| Permission | Privilege for the server to access the data disk, including: ALLOW (read/write allowed), BAN (write not allowed), or UNDEFINED (undefined). |

### ycsctl query disk

This command is used to view the information of the SYSTEM disk group.  

The command format is as follows:

```shell
ycsctl query disk
```

***Example***

```shell
$ ycsctl query disk
ID |STATUS   |PATH                             |DG                              
0  ONLINE    /dev/yfs/sdd                      SYSTEM                          
1  ONLINE    /dev/yfs/sdc                      SYSTEM                          
2  ONLINE    /dev/yfs/sdf                      SYSTEM
```

The following describes each configuration item:

| Field Name       |Meaning                  |
| ------------ | -------------------------------- |
| ID | The sequence number of the disk in the SYSTEM DiskGroup. |
| STATUS | The status of the disk in the SYSTEM DiskGroup. |
| PATH | The path of the disk in the SYSTEM DiskGroup. |
| DG | The name of the SYSTEM DiskGroup. |

## pdb add

This command is used to create a new PDB in a CDB.  

|Option |Meaning |
| --------------- | ----------------------------------------------- |
| *-c, --cluster*   | The cluster name (required)              |
| *--pdb*      | The PDB name (required)           |
| *--pdb-config*   | The [PDB configuration file](../Configuration Files/PDB Configuration File). When using yasboot to deploy YashanDB as a CDB, this file will be generated in the $YASDB_HOME directory                |
| *-u, --username*  | Specify the database user. If not specified, the default user sys is used |
| *-p, --password* | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication) (enabled by default after installation) is activated, no password needs to be specified                         |
| *-p, --password*  | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication) (enabled by default after installation) is activated, no password needs to be specified   |
| *-m, --mode*            | Syntax mode of the database, options [yashan,mysql]<br/>* yashan: Indicates creating a PDB in yashan mode, defaults to this value when omitted, and cannot be directly switched to mysql mode after creation<br/>* mysql: Indicates creating a PDB in mysql mode     |
| *-h, --help*          | View help information for the current command    |
| *-w, --nowait*   | Do not wait for command execution results after running   |
| *-d, --child*     | Display task and subtasks information   |
| *--disable*       | Disable the display of running progress  |


***Example***

```shell
$ yasboot pdb add -c yashandb --pdb pdb2 --pdb-config pdb_add.toml
```

## pdb status

This command is used to check the status of the specified PDB.

|Option |Meaning |
| --------------- | ----------------------------------------- |
| *-c, --cluster*   | The cluster name of YashanDB (required) |
| *--pdb*      | The PDB name (required)           |
| *-u, --username*  | Specify the database user. If not specified, the default user sys is used |
| *-p, --password* | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication) (enabled by default after installation) is activated, no password needs to be specified                         |
| *-p, --password*  | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication) (enabled by default after installation) is activated, no password needs to be specified   |
| *-h, --help*          | View help information for the current command    |

***Example***

```shell
$ yasboot pdb status -c yashandb --pdb pdb1
+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
| hostid   | nodeid | node_type | pdb_name | pid   | instance_status | pdb_status | pdb_role | source_node | listen_address   | data_path                      |
+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
| host0001 | 1-1:1  | cdb       | pdb1     | 9560  | open            | open       | primary  | -           | 192.168.1.2:1688 | /data/yashan/yasdb_data/db-1-1 |
+----------+--------+-----------+----------+-------+-----------------+------------+----------+-------------+--------------------+------------------------------+
| host0002 | 1-2:2  | cdb       | pdb1     | 10949 | open            | open       | standby  | 1-1:1       | 192.168.1.3:1688 | /data/yashan/yasdb_data/db-1-2 |
+----------+--------+-----------+----------+-------+-----------------+------------+----------+-------------+------------------+--------------------------------+
```

## pdb start

This command is used to start PDB(s).

|Option |Meaning |
| ------------------ | ------------------------------------------------ |
| *-c, --cluster*      | The cluster name of YashanDB (required) |
| *-n, --node-id*      | Node ID (e.g., 1-1, can be viewed using the cluster status command, no need for colon and the following number)  |
| *--pdb*      | Names of PDBs to be started, must be PDBs on the specified node. Multiple names should be separated by commas. Specifying `all` indicates starting all PDBs on the specified node  |
| *-m, --start-mode*   | Start stage, options are [mount&#124;nomount&#124;open], defaults to open   |
| *-u, --username*  | Specify the database user. If not specified, the default user sys is used |
| *-p, --password* | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication) (enabled by default after installation) is activated, no password needs to be specified                         |
| *-p, --password*  | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication) (enabled by default after installation) is activated, no password needs to be specified   |
| *-h, --help*          | View help information for the current command    |
| *-w, --nowait*   | Do not wait for command execution results after running   |
| *-d, --child*     | Display task and subtasks information   |
| *--disable*       | Disable the display of running progress  |

***Example***

```shell
$ yasboot pdb start -c yashandb -n 1-1 --pdb pdb1

$ yasboot pdb start -c yashandb -n 1-1 --pdb pdb2 -m nomount
```

## pdb restart

This command is used to restart PDBs.

|Option |Meaning |
| ------------------ | ------------------------------------------------ |
| *-c, --cluster*      | The cluster name of YashanDB (required) |
| *-n, --node-id*      | Node ID (e.g., 1-1, can be viewed using the cluster status command, no need for colon and the following number)  |
| *--pdb*      | Names of PDBs to be restarted, must be PDBs on the specified node. Multiple names should be separated by commas. Specifying 'all' indicates restarting all PDBs on the specified node  |
| *-s, --stop-mode*    | Shutdown mode, options: [normal|immediate|abort], defaults to normal   |
| *-m, --start-mode*   | Start stage, options are [mount&#124;nomount&#124;open], defaults to open    |
| *-u, --username*  | Specify the database user. If not specified, the default user sys is used |
| *-p, --password* | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication) (enabled by default after installation) is activated, no password needs to be specified                         |
| *-p, --password*  | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication) (enabled by default after installation) is activated, no password needs to be specified   |
| *-f, --force*        | Whether to forcibly stop the PDB, defaults to not forcibly stop |
| *-h, --help*          | View help information for the current command    |
| *-w, --nowait*   | Do not wait for command execution results after running   |
| *-d, --child*     | Display task and subtasks information   |
| *--disable*       | Disable the display of running progress  |

***Example***

```shell
$ yasboot pdb restart -c yashandb -n 1-1 --pdb pdb1
```

## pdb stop

This command is used to stop running PDBs.

|Option |Meaning |
| --------------- | ----------------------------------------- |
| *-c, --cluster*   | The cluster name of YashanDB (required) |
| *-n, --node-id*   | Node ID (e.g., 1-1, can be viewed using the cluster status command, no need for colon and the following number) (required) |
| *--pdb*      | Names of PDBs to be shut down, must be PDBs on the specified node. Multiple names should be separated by commas. Specifying 'all' indicates shutting down all PDBs on the specified node  |
| *-s, --stop-mode*    | Shutdown mode, options: [normal|immediate], defaults to normal   |
| *-f, --force*     | Whether to forcibly stop the PDB, defaults to not forcibly stop |
| *-u, --username*  | Specify the database user. If not specified, the default user sys is used |
| *-p, --password* | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication) (enabled by default after installation) is activated, no password needs to be specified                         |
| *-p, --password*  | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication) (enabled by default after installation) is activated, no password needs to be specified   |
| *-h, --help*          | View help information for the current command    |
| *-w, --nowait*   | Do not wait for command execution results after running   |
| *-d, --child*     | Display task and subtasks information   |
| *--disable*       | Disable the display of running progress  |

***Example***

```shell
$ yasboot pdb stop -c yashandb -n 1-1 --pdb pdb1
```

## pdb drop

This command is used to delete PDB(s).

|Option |Meaning |
| --------------- | ----------------------------------------------- |
| *-c, --cluster*   | The cluster name (required)              |
| *--pdb*      | The PDB name, multiple names separated by commas (required)           |
| *-f, --force*     | Whether the deletion operation requires secondary confirmation, defaults to requiring confirmation when omitted |
| *-h, --help*          | View help information for the current command    |
| *-w, --nowait*   | Do not wait for command execution results after running   |
| *-d, --child*     | Display task and subtasks information   |
| *--disable*       | Disable the display of running progress  |

***Example***

```shell
$ yasboot pdb drop -c yashandb --pdb pdb1
```

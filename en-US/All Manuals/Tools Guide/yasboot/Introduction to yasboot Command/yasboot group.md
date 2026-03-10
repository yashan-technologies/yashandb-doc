## group config show

This command is used to display the configuration information of the node group.

|Option |Meaning |
| ---------------- | --------------------------------- |
| *-c, --cluster*   | The cluster name of YashanDB (mandatory parameter)  |
| *-g, --group-id*  | Group ID (can be viewed through the cluster status command; the value before the hyphen in nodeid is the group-id, for example, the group ID for `1-1:1` is 1) (mandatory parameter) |
| *-q, --query*     | Query a specific parameter in the group configuration |
| *-a, --all*       | Display all parameters of the group                 |
| *--disable*       | Suppress the display of running progress information  |
| *-u, --username*  | Specify the database user. If not specified, the default user sys is used |
| *-p, --password*  | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication) (enabled by default after installation) is activated, no password needs to be specified   |

***Example***

```shell
$ yasboot group config show -c yashandb -g 1
 mn-1-1                                   
------------------------------------------
 CONTROL_FILES=('?/dbfiles/ctrl1', '?/dbf 
 iles/ctrl2', '?/dbfiles/ctrl3')          
------------------------------------------
 DDL_LOCK_TIMEOUT=60                      
------------------------------------------
 DEFAULT_TABLE_TYPE=LSC                   
------------------------------------------
 DIN_ADDR=192.168.1.2:1679              
------------------------------------------
 LISTEN_ADDR=192.168.1.2:1678           
------------------------------------------
 NODE_ID=1-1:1                            
------------------------------------------
 RUN_LOG_LEVEL=DEBUG                      
------------------------------------------
```

## group config set

This command is used to set the configuration of the node group.

|Option |Meaning |
| ------------------ | ----------------------------------- |
| *-c, --cluster*      | The cluster name of YashanDB (mandatory parameter)  |
| *-g, --group-id*     | Group ID (can be viewed through the cluster status command; the value before the hyphen in nodeid is the group-id, for example, the group ID for `1-1:1` is 1) |
| *-gt, --group-type*  | Group type name: db: Standalone Deployment, ce: YAC/Distributed Cluster, mn: Distributed MN Group, cn: Distributed CN Node, dn: Distributed DN Group |
| *-k, --key*          | The name of the parameter to be set (mandatory), standalone mode supports modifying PROTECTION_MODE |
| *-v, --value*        | The value corresponding to the parameter key (mandatory) |
| *-f, --force*        | Force setting the configuration parameters            |
| *-r, --rollback*     | Set rollback parameter (mutually exclusive with force) |
| *-s, --scope*        | Support setting the scope of the parameter           |
| *-w, --nowait*       | Do not wait for the execution result after the command runs |
| *-d, --child*        | Display task and sub-task information                 |
| *--disable*          | Suppress the display of the task progress bar        |
| *-u, --username*  | Specify the database user. If not specified, the default user sys is used |
| *-p, --password*  | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication) (enabled by default after installation) is activated, no password needs to be specified   |

Either the `-g` or `-gt` parameter must be selected.

***Example***

```shell
$ yasboot group config set -k MAX_WORKERS -v 10 -c yashandb -g 1
+--------------------------------------------------------------------------------------------------------------+
| type | uuid             | name                 | hostid | index    | status  | return_code | progress | cost |
+--------------------------------------------------------------------------------------------------------------+
| task | b61ac050e4bdd753 | GroupParentConfigSet | -      | yashandb | SUCCESS | 0           | 100      | 1    |
+------+------------------+----------------------+--------+----------+---------+-------------+----------+------+
task completed, status: SUCCESS
```

Change the group type to dn nodes.

```shell
$ yasboot group config set -k MAX_WORKERS -v 1024 -c yashandb -gt dn
```

Forcefully change the configuration parameters of the group node.

```shell
$ yasboot group config set -k MAX_WORKERS -v 1024 -c yashandb -g 3 --force
```

Set to roll back after modifying the group node parameters fails.

```shell
$ yasboot group config set -k MAX_WORKERS -v 1024 -c yashandb -gt dn -r
```

## group config unset

This command is used to restore the node group configuration to the default value of the database, where [database creation parameters](../Database Creation Parameters) may differ from the defaults assigned during deployment.

|Option |Meaning |
| ---------------- | --------------------------------- |
| *-c, --cluster*   | The cluster name of YashanDB (mandatory parameter)  |
| *-g, --group-id*  | Group ID (can be viewed through the cluster status command; the value before the hyphen in nodeid is the group-id, for example, the group ID for `1-1:1` is 1) (mandatory parameter) |
| *-k, --key*       | The name of the parameter to be removed (mandatory) |
| *-w, --nowait*    | Do not wait for the execution result after the command runs |
| *-d, --child*     | Display task and sub-task information                |
| *--disable*       | Suppress the display of the task progress bar       |
| *-u, --username*  | Specify the database user. If not specified, the default user sys is used |
| *-p, --password*  | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication) (enabled by default after installation) is activated, no password needs to be specified   |

***Example***

```shell
$ yasboot group config unset -c yashandb -g 1 -k SLOW_LOG_SQL_MAX_LEN 
```

## group status

This command is used to view the running status of the nodes in the group.

|Option |Meaning |
| ---------------- | ----------------------------------- |
| *-c, --cluster*   | The cluster name of YashanDB (mandatory parameter)  |
| *-g, --group-id*  | Group ID (can be viewed through the cluster status command; the value before the hyphen in nodeid is the group-id, for example, the group ID for `1-1:1` is 1)   |
| *-d, --detail*    | Whether to display the details of the running status, default is false |
| *-u, --username*  | Specify the database user. If not specified, the default user sys is used |
| *-p, --password*  | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication) (enabled by default after installation) is activated, no password needs to be specified   |

***Example***

```shell
$ yasboot group status -c yashandb -g 1 -d
```

## group start

This command is used to start all nodes in the group.

|Option |Meaning |
| ------------------ | ------------------------------------------------ |
| *-c, --cluster*      | The cluster name of YashanDB (mandatory parameter)  |
| *-g, --group-id*     | Group ID (can be viewed through the cluster status command; the value before the hyphen in nodeid is the group-id, for example, the group ID for `1-1:1` is 1) (mandatory parameter) |
| *-m, --start-mode*   | Start stage, options are [mount|nomount|open], defaults to open <br/>In YAC/Distributed Cluster Deployment, starting to the MOUNT stage is not supported.   |
| *-w, --nowait*       | Do not wait for the execution result after the command runs |
| *-d, --child*        | Display task and sub-task information                |
| *--disable*          | Suppress the display of running progress information  |
| *-u, --username*  | Specify the database user. If not specified, the default user sys is used |
| *-p, --password*  | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication) (enabled by default after installation) is activated, no password needs to be specified   |
| *--wait-timeout*     | Command execution timeout (hidden parameter)        |
| *--wallet-password*   | Database wallet password                             |

***Example***

```shell
$ yasboot group start -c yashandb -g 1 -m nomount
```

## group stop

This command is used to stop all nodes in the group.

|Option |Meaning |
| ---------------- | --------------------------------- |
| *-c, --cluster*      | The cluster name of YashanDB (mandatory parameter)  |
| *-g, --group-id*     | Group ID (can be viewed through the cluster status command; the value before the hyphen in nodeid is the group-id, for example, the group ID for `1-1:1` is 1) (mandatory parameter) |
| *-f, --force*        | Whether to forcibly stop the nodes, default is not forced |
| *-w, --nowait*       | Do not wait for the execution result after the command runs |
| *-d, --child*        | Display task and sub-task information                |
| *--disable*          | Suppress the display of running progress information  |
| *-s, --stop-mode*    | Shutdown mode, optional [normal|immediate|abort], default is immediate (not currently available for YAC/Distributed Cluster Deployment) |
| *-u, --username*  | Specify the database user. If not specified, the default user sys is used |
| *-p, --password*  | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication) (enabled by default after installation) is activated, no password needs to be specified   |
| *--wait-timeout*     | Command execution timeout (hidden parameter)        |

***Example***

```shell
$ yasboot group stop -c yashandb -g 1
```

## group restart

This command is used to restart all nodes in the group.

|Option |Meaning |
| ------------------ | ------------------------------------------------ |
| *-c, --cluster*      | The cluster name of YashanDB (mandatory parameter)  |
| *-g, --group-id*     | Group ID (can be viewed through the cluster status command; the value before the hyphen in nodeid is the group-id, for example, the group ID for `1-1:1` is 1) (mandatory parameter) |
| *-m, --start-mode*   | Start stage, options are [mount|nomount|open], defaults to open <br/>In YAC/Distributed Cluster Deployment, starting to the MOUNT stage is not supported.   |
| *-f, --force*        | Whether to forcibly stop the nodes, default is not forced |
| *-w, --nowait*       | Do not wait for the execution result after the command runs |
| *-d, --child*        | Display task and sub-task information                |
| *--disable*          | Suppress the display of running progress information  |
| *-s, --stop-mode*    | Shutdown mode, optional [normal|immediate|abort], default is immediate (not currently available for YAC/Distributed Cluster Deployment) |
| *-u, --username*  | Specify the database user. If not specified, the default user sys is used |
| *-p, --password*  | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication) (enabled by default after installation) is activated, no password needs to be specified   |
| *--wait-timeout*     | Command execution timeout (hidden parameter)        |
| *--wallet-password*   | Database wallet password                             |

***Example***

```shell
$ yasboot group restart -c yashandb -g 1 -f -m mount
```

## group add

This command is used to expand the DN node group in ISC Distributed Cluster Deployment.

|Option |Meaning |
| --------------- |-------------------------|
| *-c, --cluster*   | Cluster name (required)                      |
| *-t, --toml*      | Path to the database scaling configuration file `{cluster_name}_add.toml` generated by the yasboot config group gen command (required)          |
| *-d, --child*     | Display task and sub-task information              |
| *--disable*       | Suppress the display of running progress information |
| *--auto-redistribute* | Whether to automatically redistribute, default is true. If set to false, manual execution of yasboot dataspace redistribute is required after expanding the DN group; for command details, please refer to [yasboot dataspace](yasboot dataspace) |
| *--clean-residual-immediately* | Whether to automatically clean up chunk table space |
| *--dataspace-id*  | Dataspace id (DS_ID in route$), default is 0     |
| *-p, --password*  | The password corresponding to the `sys` user of the database<br/>If [OS authentication](../../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication) (enabled by default after installation) is activated, no password needs to be specified |
| *--wait-timeout*  | Command execution timeout (hidden parameter)      |

***Example***

```shell
$ yasboot group add -t yashandb_add.toml -c yashandb
```

## group remove

This command is used to reduce the DN node group in ISC Distributed Cluster Deployment.

|Option |Meaning |
|-----------------|-------------------------------------------|
| *-c, --cluster*      | The cluster name of YashanDB                      |
| *--group-ids*        | Group ID (can be viewed through the cluster status command; the value before the hyphen in nodeid is the group-id, for example, the group ID for `1-1:1` is 1), supports multiple, separated by commas |
| *-f, --force*        | Skip confirmation and execute the command directly |
| *--purge*            | Delete the data of the reduced nodes             |
| *-w, --nowait*       | Do not wait for the execution result after the command runs |
| *-d, --child*        | Display task and sub-task information              |
| *--disable*          | Suppress the display of running progress information |
| *--clean*            | Clean up all failed node groups, default is false |
| *--dataspace-id*     | Dataspace id (DS_ID in route$), default is 0     |
| *-u, --username*  | Specify the database user. If not specified, the default user sys is used |
| *-p, --password*  | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication) (enabled by default after installation) is activated, no password needs to be specified   |
| *--with-host*        | After successfully deleting the node group, continue to delete the empty server information with no database nodes and no yasom processes in the current cluster (this parameter is ineffective if --nowait is used) |

***Example***

```shell
$ yasboot group remove -c yashandb --group-ids 3 --purge -f
```

## group auto-election on

This command is used to enable leader election for the node group, applicable only to Standalone Deployment and ISC Distributed Cluster Deployment.

|Option |Meaning |
|-----------------|-------------------------------------------|
| *-c, --cluster*      | The cluster name of YashanDB                     |
| *-g, --group-id*     | Group ID (can be viewed through the cluster status command; the value before the hyphen in nodeid is the group-id, for example, the group ID for `1-1:1` is 1) (mandatory parameter) |
| *-w, --nowait*       | Do not wait for the execution result after the command runs |
| *-d, --child*        | Display task and sub-task information             |
| *--disable*          | Suppress the display of running progress information |
| *-u, --username*  | Specify the database user. If not specified, the default user sys is used |
| *-p, --password*  | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication) (enabled by default after installation) is activated, no password needs to be specified   |

***Example***

```shell
$ ./bin/yasboot group auto-election on -c yashandb --group-id 2
```

## group auto-election off

This command is used to disable leader election for the node group, applicable only to Standalone Deployment and ISC Distributed Cluster Deployment.

|Option |Meaning |
|-----------------|-------------------------------------------|
| *-c, --cluster*      | The cluster name of YashanDB                     |
| *-g, --group-id*     | Group ID (can be viewed through the cluster status command; the value before the hyphen in nodeid is the group-id, for example, the group ID for `1-1:1` is 1) (mandatory parameter) |
| *-w, --nowait*       | Do not wait for the execution result after the command runs |
| *-d, --child*        | Display task and sub-task information             |
| *--disable*          | Suppress the display of running progress information |
| *-u, --username*  | Specify the database user. If not specified, the default user sys is used |
| *-p, --password*  | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication) (enabled by default after installation) is activated, no password needs to be specified   |

***Example***

```shell
$ ./bin/yasboot group auto-election off -c yashandb --group-id 2
```

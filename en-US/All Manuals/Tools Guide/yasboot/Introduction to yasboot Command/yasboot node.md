## node config show

This command is used to display the configuration information of a specified node.

|Option |Meaning |
| ----------------- | ---------------------------------- |
| *-c, --cluster*   | The cluster name of YashanDB (required) |
| *-n, --node-id*   | The ID of the target node (e.g., `1-2`. You can view the database information through the `yasboot cluster status` command and take the numeric string before the colon of `nodeid`) (required) |
| *-q, --query*     | Query single or multiple parameters in the group configuration (supports fuzzy matching) |
| *--parameter*     | Details of the configuration             |
| *-a, --all*       | Query all parameters, including those not set |
| *-u, --username*  | Specify the database user. If not specified, the default user sys is used |
| *-p, --password*  | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证) (enabled by default after installation) is activated, no password needs to be specified   |

***Example***

```shell
$ yasboot node config show -c yashandb --node-id 1-1 -a
$ yasboot node config show -c yashandb --node-id 1-1 -q SCOL_DATA_BUFFER_SIZE -a
```

## node config set

This command is used to manually change/set the configuration of a specified node.

|Option |Meaning |
| --------------- | ---------------------------------- |
| *-c, --cluster*   | The cluster name of YashanDB (required) |
| *-n,--node-id*    | The ID of the target node (e.g., `1-2`. You can view the database information through the `yasboot cluster status` command and take the numeric string before the colon of `nodeid`) (required) |
| *-k, --key*       | Name of the parameter to be set (required), standalone supports modifying PROTECTION_MODE |
| *-v, --value*     | Value corresponding to the parameter key (required) |
| *-s, --scope*     | Supports setting the scope of the parameter |
| *-w, --nowait*    | Do not wait for the execution result after running |
| *-d, --child*     | Display task and subtasks information   |
| *--disable*       | Disable the display of task progress     |
| *-u, --username*  | Specify the database user. If not specified, the default user sys is used |
| *-p, --password*  | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证) (enabled by default after installation) is activated, no password needs to be specified   |

***Example***

```shell
$ yasboot node config set -c yashandb --node-id 1-1 -k UNDO_RETENTION -v 350
```

## node config unset

This command is used to reset the configuration information of a specified node to its initial default values.

|Option |Meaning |
| --------------- | ----------------------------------------- |
| *-c, --cluster*   | The cluster name of YashanDB (required) |
| *-n, --node-id*   | The ID of the target node (e.g., `1-2`. You can view the database information through the `yasboot cluster status` command and take the numeric string before the colon of `nodeid`) (required) |
| *-k, --key*       | Name of the parameter to be reset (required) |
| *-w, --nowait*    | Do not wait for the execution result after running |
| *-d, --child*     | Display task and subtasks information   |
| *--disable*       | Disable the display of task progress     |
| *-u, --username*  | Specify the database user. If not specified, the default user sys is used |
| *-p, --password*  | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证) (enabled by default after installation) is activated, no password needs to be specified   |

***Example***

```shell
$ yasboot node config unset -c yashandb --node-id 1-1 -k UNDO_RETENTION
```

## node status

This command is used to check the status of a specified node.

|Option |Meaning |
| --------------- | ----------------------------------------- |
| *-c, --cluster*   | The cluster name of YashanDB (required) |
| *-n, --node-id*   | The ID of the target node (e.g., `1-2`. You can view the database information through the `yasboot cluster status` command and take the numeric string before the colon of `nodeid`) (required) |
| *-d, --detail*    | Whether to display details of the running status, defaults to false |
| *-u, --username*  | Specify the database user. If not specified, the default user sys is used |
| *-p, --password*  | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证) (enabled by default after installation) is activated, no password needs to be specified   |

***Example***

```shell
$ yasboot node status -c yashandb -n 1-1
+---------------------------------------+
| host_id  | node_type | nodeid | pid   |
+---------------------------------------+
| host0001 | mn        | 1-1:1  | 22211 |
+----------+-----------+--------+-------+
```

## node stop

This command is used to stop a running node.

|Option |Meaning |
| --------------- | ----------------------------------------- |
| *-c, --cluster*   | The cluster name of YashanDB (required) |
| *-n, --node-id*   | The ID of the target node (e.g., `1-2`. You can view the database information through the `yasboot cluster status` command and take the numeric string before the colon of `nodeid`) (required) |
| *-f, --force*     | Whether to forcibly stop the node, defaults to not forcibly stop |
| *-w, --nowait*    | Do not wait for the execution result after running |
| *-d, --child*     | Display task and subtasks information   |
| *--disable*       | Disable the display of running progress  |
| *-s, --stop-mode* | Shutdown mode, options are [normal|immediate|abort], defaults to immediate (not available for YAC/Distributed Cluster Deployment) |
| *-u, --username*  | Specify the database user (defaults to sys user if not specified) |
| *-p, --password*  | Password corresponding to the database user |
| *--wait-timeout*  | Command execution timeout (hidden parameter) |

***Example***

```shell
$ yasboot node stop -c yashandb -n 4-1
```

## node start

This command is used to start a node.

|Option |Meaning |
| ------------------ | ------------------------------------------------ |
| *-c, --cluster*      | The cluster name of YashanDB (required) |
| *-n, --node-id*      | The ID of the target node (e.g., `1-2`. You can view the database information through the `yasboot cluster status` command and take the numeric string before the colon of `nodeid`) (required) |
| *-m, --start-mode*   | Start mode, options are [mount|nomount|open], defaults to open (not available for YAC/Distributed Cluster Deployment to start in mount mode) |
| *-w, --nowait*       | Do not wait for the execution result after running |
| *-d, --child*        | Display task and subtasks information   |
| *--disable*          | Disable the display of running progress  |
| *-u, --username*  | Specify the database user. If not specified, the default user sys is used |
| *-p, --password*  | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证) (enabled by default after installation) is activated, no password needs to be specified   |
| *--wait-timeout*     | Command execution timeout (hidden parameter) |
| *--wallet-password*   | Database wallet password                  |

***Example***

```shell
$ yasboot node start -c yashandb -n 4-1
$ yasboot node start -c yashandb -n 4-1 -m nomount
```

## node restart

This command is used to restart a node.

|Option |Meaning |
| ------------------ | ------------------------------------------------ |
| *-c, --cluster*      | The cluster name of YashanDB (required) |
| *-n, --node-id*      | The ID of the target node (e.g., `1-2`. You can view the database information through the `yasboot cluster status` command and take the numeric string before the colon of `nodeid`) (required) |
| *-m, --start-mode*   | Start mode, options are [mount|nomount|open], defaults to open (not available for YAC/Distributed Cluster Deployment to start in mount mode) |
| *-f, --force*        | Whether to forcibly stop the node, defaults to not forcibly stop |
| *-w, --nowait*       | Do not wait for the execution result after running |
| *-d, --child*        | Display task and subtasks information   |
| *--disable*          | Disable the display of running progress  |
| *-s, --stop-mode*    | Shutdown mode, options are [normal|immediate|abort], defaults to immediate (not available for YAC/Distributed Cluster Deployment) |
| *-u, --username*     | Specify the database user (defaults to sys user if not specified) |
| *-p, --password*     | Password corresponding to the database user |
| *--wait-timeout*     | Command execution timeout (hidden parameter) |
| *--wallet-password*   | Database wallet password                  |

***Example***

```shell
$ yasboot node restart -c yashandb -n 4-1
```

## node switchover

This command is used to manually perform a switchover between primary/standby (only applicable to primary/standby YACs).

|Option |Meaning |
| --------------- | ------------------------------------------------- |
| *-c, --cluster*   | The cluster name of YashanDB (required) |
| *-n, --node-id*   | Node ID to be promoted to primary (e.g., 1-1, can be viewed using the cluster status command, no need for colon and the following number) (required) |
| *-f, --force*     | Whether to confirm, defaults to not     |
| *-w, --nowait*    | Do not wait for the execution result after running |
| *-d, --child*     | Display task and subtasks information   |
| *--disable*       | Disable the display of running progress  |
| *-u, --username*  | Specify the database user. If not specified, the default user sys is used |
| *-p, --password*  | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证) (enabled by default after installation) is activated, no password needs to be specified   |
| *--wait-timeout*  | Command execution timeout (hidden parameter) |

***Example***

```shell
$ yasboot node switchover -c yashandb -n 4-1
```

## node failover

This command is used to manually perform a failover between primary/standby (only applicable to primary/standby YACs).

|Option |Meaning |
| --------------- | ------------------------------------------------- |
| *-c, --cluster*   | The cluster name of YashanDB (required) |
| *-n, --node-id*   | Node ID to be promoted to primary (e.g., 1-1, can be viewed using the cluster status command, no need for colon and the following number) (required) |
| *-w, --nowait*    | Do not wait for the execution result after running |
| *-d, --child*     | Display task and subtasks information   |
| *--disable*       | Disable the display of running progress  |
| *-u, --username*  | Specify the database user. If not specified, the default user sys is used |
| *-p, --password*  | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证) (enabled by default after installation) is activated, no password needs to be specified   |
| *--wait-timeout*  | Command execution timeout (hidden parameter) |

***Example***

```shell
$ yasboot node failover -c yashandb -n 4-1
```

## node remove

This command is used for node-level scaling down. The node(s) may be:

- The standby database(s) in Standalone (Primary-Standby) Deployment

- The instance(s) in YAC Deployment

- The CN node(s), DN standby node(s) or MN standby node(s) in ISC Distributed Cluster Deployment

|Option |Meaning |
| --------------- | ---------------------------------------- |
| *-c, --cluster*   | The cluster name of YashanDB (required) |
| *-n, --node-id*   | The ID of the node to be deleted (e.g., `1-2`. You can view the database information through the `yasboot cluster status` command and take the numeric string before the colon of `nodeid`). Only one ID is allowed.<br/>To delete multiple nodes at once, please use the `--node-ids` parameter.|
| *--node-ids*      | List of node IDs to delete (e.g., `1-3,1-4`. You can view the database information through the `yasboot cluster status` command and take the numeric string before the colon of `nodeid`). One or more IDs are allowed to be specified, separated by commas. |
| *-f, --force*     | Skip confirmation and execute the command directly |
| *--purge*         | Delete the data of the target node       |
| *-w, --nowait*    | Do not wait for the execution result after running |
| *-d, --child*     | Display task and subtasks information   |
| *--disable*       | Disable the display of running progress  |
| *--clean*         | Clean up all nodes with failed expansions, defaults to false |
| *--ce-clean*         | Delete redundant files after backup restoration. This parameter applies only to YAC deployment |
| *--no-primary*    | Whether to allow operations without a primary database in a standalone primary/standby deployment (this parameter is only valid for Standalone Deployment), defaults to false |
| *-u, --username*  | Specify the database user. If not specified, the default user sys is used |
| *-p, --password*  | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证) (enabled by default after installation) is activated, no password needs to be specified   |
| *--with-host*     | After successfully deleting the node, continue to delete the information of empty servers in the current cluster with no database nodes and no yasom processes (this parameter becomes invalid if --nowait is used) |
| *--with-unconnected-host* | If the server of the deleted node is no longer connected, use this parameter to skip some tasks and forcibly delete the node.  |

***Example***

```shell
$ yasboot node remove -c yashandb -n 4-1 --purge
```

## node add

This command is used for node-level scaling up. The node(s) may be:

- The standby database(s) in Standalone (Primary-Standby) Deployment

- The instance(s) in YAC Deployment

- The CN node(s) or DN node(s) in Distributed Cluster Deployment

- The CN node(s), DN standby node(s) or MN standby node(s) in ISC Distributed Cluster Deployment

|Option |Meaning |
| --------------- | ----------------------------------------------- |
| *-c, --cluster*   | The cluster name (required)              |
| *-t, --toml*      | Path to the database scaling configuration file `{cluster_name}_add.toml` generated by the yasboot config node gen command (required) |
| *-d, --child*     | Display task and subtasks information   |
| *--disable*       | Disable the display of running progress  |
| *--no-primary*    | Whether to allow operations without a primary database in a standalone primary/standby deployment (this parameter is only valid for Standalone Deployment), defaults to false |
| *-p, --password*  | Password corresponding to the `sys` user of the database<br/>If [OS authentication](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证) (enabled by default after installation) is activated, no password needs to be specified |
| *--max-protection-timeout* | Modify the timeout for protection mode (in seconds), default value is 7200, if not modified successfully after this time, the task is considered failed and the operation is interrupted |
| *--wait-timeout*  | Command execution timeout (hidden parameter) |
| *--no-backend*    | Run all background tasks in the foreground |
| *--wallet-password* | The password of database wallet                  |

***Example***

```shell
$ yasboot node add -t yashandb_add.toml -c yashandb -d
```

## node build

This command is used to rebuild standby nodes.

|Option |Meaning |
| --------------- |------------------------------|
| *-c, --cluster*   | The cluster name (required)              |
| *-w, --nowait*    | Do not wait for the execution result after running |
| *-d, --child*     | Display task and subtasks information   |
| *--node-ids*      | Node IDs to be rebuilt, separated by commas (required) |
| *--disable*       | Disable the display of running progress  |
| *-u, --username*  | Specify the database user. If not specified, the default user sys is used |
| *-p, --password*  | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证) (enabled by default after installation) is activated, no password needs to be specified   |
| *--wait-timeout*  | Command execution timeout (hidden parameter) |
| *-f, --force*     | No confirmation required, defaults to false |
| *--parallelism*   | Degree of parallelism, range 1-16, defaults to 4 |
| *--skip-validate* | If this parameter is specified, it indicates that the pre-check when creating the standby database should be skipped, default is to check |
| *--overwrite*     | If this parameter is specified, it indicates that files with the same name will be overwritten during the creation of the standby database (REDO files and archive files will not be overwritten, manual inspection and cleaning are required), default is not to overwrite |
| *--wallet-password* | The password of database wallet                  |

***Example***

```shell
$ yasboot node build -c yashandb --node-ids 1-2,1-3
```

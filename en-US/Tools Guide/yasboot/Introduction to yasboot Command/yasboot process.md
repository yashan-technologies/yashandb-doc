## process yasom status

This command is used to check the status of the yasom process.

|Option |Meaning |
| --------------- | :--------------------------------------------------- |
| *-c, --cluster*   | The cluster name of YashanDB (mandatory parameter)                                      |
| *-t, --toml*      | Check the current yasom status on all servers through the server configuration file `hosts.toml` |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
$ yasboot process yasom status -c yashandb
$ yasboot process yasom status -c yashandb -t hosts.toml
```

## process yasom stop

This command is used to stop the local yasom process.

|Option |Meaning |
| --------------- |:------------------------------|
| *-c, --cluster*   | The cluster name of YashanDB (mandatory parameter)           |
| *-t, --toml*      | Stop the current yasom on all servers through the server configuration file `hosts.toml`  |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
$ yasboot process yasom stop -c yashandb
$ yasboot process yasom stop -c yashandb -t hosts.toml
```

## process yasom start

This command is used to start the yasom process.

|Option |Meaning |
| --------------- | :----------------------------------------------- |
| *-c, --cluster*   | The cluster name of YashanDB (mandatory parameter)                                  |
| *-t, --toml*      | Start the current yasom on all servers through the server configuration file `hosts.toml` |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
$ yasboot process yasom start -c yashandb
$ yasboot process yasom start -c yashandb -t hosts.toml
```

## process yasom restart

This command is used to restart the yasom process.

|Option |Meaning |
| --------------- | :----------------------------------------------- |
| *-c, --cluster*   | The cluster name of YashanDB (mandatory parameter)                                  |
| *-t, --toml*      | Restart the current yasom on all servers through the server configuration file `hosts.toml` |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
$ yasboot process yasom restart -c yashandb
$ yasboot process yasom restart -c yashandb -t hosts.toml
```

## process yasom recover

This command is used to recover a yasom process on the current server from a backup set.

|Option |Meaning |
| ---------------- | ------------------------------------------------------------ |
| *-c, --cluster*    | The cluster name of YashanDB (mandatory parameter)         |
| *-m, --meta*       | Path to the backup set file, default is install_path/om/{cluster}/data/backup. The latest backup set is selected by default |
| *-l,--listen*      | Listening port of the yasom process                         |
| *-f,--force*       | Skip confirmation and recover directly                      |
| *--role*           | Role of the recovered yasom, optional [primary, secondary], default is secondary  |
| *--force-create*   | Force to use the old backup set to recover yasom, not recommended to use |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
# Recover a secondary yasom on a server without yasom
$ yasboot process yasom recover -c yashandb -l 192.168.1.3:1675

# Primary yasom crashed, recover a primary yasom on a server without yasom
$ yasboot process yasom recover -c yashandb -l 192.168.1.3:1675 --role primary

# Primary yasom crashed, promote a secondary yasom on a server with an existing secondary yasom
$ yasboot process yasom recover -c yashandb --role primary
```

## process yasom clean

This command is used to clean the yasom process on the current server.

|Option |Meaning |
| --------------- | ---------------------------- |
| *-c, --cluster*   | The cluster name of YashanDB (mandatory parameter) |
| *-f,--force*      | Skip confirmation and clean directly  |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
$ yasboot process yasom clean -c yashandb
```

## process yasom sync

This command is used to synchronize yasom configurations across all servers, mainly to resolve data inconsistency issues caused by network reasons when executing yasom recovery and cleaning commands.

|Option |Meaning |
| --------------- | ---------------------------- |
| *-c, --cluster*   | The cluster name of YashanDB (mandatory parameter) |
| *-f,--force*      | Skip confirmation and sync directly  |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
$ yasboot process yasom sync -c yashandb
```

## process yasom repair on|off

This command is used to configure the self-repair functionality of the current yasom.

After enabling the self-repair functionality, when the database cluster is normal but a yasom exhibits a multi-master phenomenon, it will automatically repair by keeping one master yasom and demoting the remaining yasoms automatically. The demoted yasom will back up its own metadata to $YASDB_HOME/om/{cluster_name}/data/repair/.

It is recommended that all yasom self-repair configurations remain consistent. For specific configuration operations, please refer to [yasom configuration](../Manage yasom/Managing Primary-Secondary yasom).

If the database cluster information does not meet expectations after self-repair, you can use the yasboot cluster load command to reload the information.

Using self-repair must follow the rules below:

- *yasom* election and self-repair are mutually exclusive.

- The [OS authentication](../../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication) must be enabled (it is enabled by default when following standard installation steps) to properly use the self-repair functionality.

|Option |Meaning |
|-----------------| ---------------------------- |
| *-c, --cluster*   | The cluster name of YashanDB (mandatory parameter) |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
# Enable self-repair
$ yasboot process yasom repair on -c yashandb

# Disable self-repair
$ yasboot process yasom repair off -c yashandb
```

## process yasom demote

This command is used to demote yasom itself.

|Option |Meaning |
|-----------------| ---------------------------- |
| *-c, --cluster*   | The cluster name of YashanDB (mandatory parameter) |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
$  yasboot process yasom demote -c yashandb
```

## process yasom config set

This command is used to configure yasom automatic leader election parameters, which take effect immediately after configuration.

| Option | Meaning |
|-----------------| ---------------------------- |
| *-c, --cluster* | The cluster name of YashanDB (mandatory parameter) |
| *-h,--help*        | View help information for the current command |
| *--heartbeat-interval*        | Heartbeat interval for yasom automatic leader election |
| *--heartbeat-timeout*        | Heartbeat timeout for yasom automatic leader election |
| *--heartbeat-db-url*        | YCM backend database connection address (ip:port). For primary-standby deployment, specify multiple addresses: ip1:port1,ip2:port2,ip3:port3 |
| *--heartbeat-db-user*        | YCM backend database username |
| *--heartbeat-db-password*        | YCM backend database password. The password will be encrypted and saved |

## process yasom config show

This command is used to query yasom automatic leader election parameters.

| Option | Meaning |
|-----------------| ---------------------------- |
| *-c, --cluster* | The cluster name of YashanDB (mandatory parameter) |
| *-h,--help*        | View help information for the current command |

## process yasom election on

This command is used to enable yasom automatic leader election.

| Option | Meaning |
|-----------------| ---------------------------- |
| *-c, --cluster* | The cluster name of YashanDB (mandatory parameter) |
| *-h,--help*        | View help information for the current command |

## process yasom election off

This command is used to disable yasom automatic leader election.

| Option | Meaning |
|-----------------| ---------------------------- |
| *-c, --cluster* | The cluster name of YashanDB (mandatory parameter) |
| *-h,--help*        | View help information for the current command |

## process yasagent status

This command is used to check the running status of yasagent.

|Option |Meaning |
| --------------- | :----------------------------------------------------------- |
| *-c, --cluster*   | The cluster name of YashanDB (mandatory parameter)                             |
| *-t, --toml*      | The `hosts.toml` server configuration file generated during the current database installation has no practical effect  |
| *--hostid*        | The `hostid` of the target server, must be specified as the `hostid` recorded in the `hosts.toml` file.<br>Used to query the runtime status of the yasagent process on the target server. |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
# View the runtime status of yasagent process on all nodes
$ yasboot process yasagent status -c yashandb

# View the runtime status of yasagent process on server host0002
$ yasboot process yasagent status -c yashandb --hostid host0002
```

## process yasagent stop

This command is used to stop the running yasagent process, supporting stopping agents on other servers.

|Option |Meaning |
| --------------- | :----------------------------------------------------------- |
| *-c, --cluster*   | The cluster name of YashanDB (mandatory parameter)                             |
| *-t, --toml*      | The `hosts.toml` server configuration file generated during current database installation <br/>Used to stop the yasagent process on all nodes of the current database |
| *--hostid*        | The `hostid` of the target server must be used in conjunction with the `-t` parameter and must be specified as the `hostid` recorded in the `hosts.toml` file <br/>Used to stop the yasagent process on the target server |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
# Stop the yasagent process on the current server
$ yasboot process yasagent stop -c yashandb

# Stop the yasagent process on all nodes
$ yasboot process yasagent stop -c yashandb -t hosts.toml

# Stop the yasagent process on server host0002
$ yasboot process yasagent stop -c yashandb -t hosts.toml --hostid host0002
```

## process yasagent start

This command is used to start the server's yasagent process, supporting starting yasagents on other servers.

|Option |Meaning |
| --------------- | :----------------------------------------------------------- |
| *-c, --cluster*   | The cluster name of YashanDB (mandatory parameter)                             |
| *-t, --toml*      | The `hosts.toml` server configuration file generated during current database installation <br/>Used to start the yasagent process on all nodes of the current database |
| *--hostid*        | The `hostid` of the target server must be used in conjunction with the `-t` parameter and must be specified as the `hostid` recorded in the `hosts.toml` file <br/>Used to start the yasagent process on the target server |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
# Start the yasagent process on the current server
$ yasboot process yasagent start -c yashandb

# Start the yasagent process on all nodes
$ yasboot process yasagent start -c yashandb -t hosts.toml

# Start the yasagent process on server host0002
$ yasboot process yasagent start -c yashandb -t hosts.toml --hostid host0002
```

## process yasagent restart

This command is used to restart the server's yasagent process, supporting restarting yasagent processes on other servers.

|Option |Meaning |
| --------------- | :----------------------------------------------------------- |
| *-c, --cluster*   | The cluster name of YashanDB (mandatory parameter)                             |
| *-t, --toml*      | The `hosts.toml` server configuration file generated during current database installation <br/>Used to restart the yasagent process on all nodes of the current database |
| *--hostid*        | The `hostid` of the target server must be used in conjunction with the `-t` parameter and must be specified as the `hostid` recorded in the `hosts.toml` file <br/>Used to restart the yasagent process on the target server |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
# Restart the yasagent process on the current server
$ yasboot process yasagent start -c yashandb

# Restart the yasagent process on all nodes
$ yasboot process yasagent stop -c yashandb -t hosts.toml

# Restart the yasagent process on server host0002
$ yasboot process yasagent start -c yashandb -t hosts.toml --hostid host0002
```

## process yasdb status

This command is used to check the status of the local yasdb process.

|Option |Meaning |
| --------------- | :---------------------------------------------------------- |
| *-c, --cluster*   | The cluster name corresponding to the yasdb process state to be checked, limited to clusters deployed on the local server (mandatory parameter) |
| *-u, --username*  | Specify the database user. If not specified, the default user sys is used |
| *-p, --password*  | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication) (enabled by default after installation) is activated, no password needs to be specified   |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
$ yasboot process yasdb status -c yashandb
```

## process yasdb stop

This command is used to stop the yasdb process on the local server.

|Option |Meaning |
| --------------- | :------------------------------------------------------ |
| *-c, --cluster*   | The cluster name of YashanDB (mandatory parameter)                                |
| *-n, --node-id*   | Stop a single node on this server by specifying the ID of the target node (e.g., `1-2`. You can view the database information through the `yasboot cluster status` command and take the numeric string before the colon of `nodeid`) |
| *-f, --force*     | Force stop the node, default is non-forced                            |
| *-s, --stop-mode* | Shutdown mode, optional [normal|immediate|abort], default is immediate (YAC/Distributed Cluster Deployment temporarily cannot be used)   |
| *-u, --username*  | Specify the database user (uses sys user by default if not specified) |
| *-p, --password*  | Password corresponding to the database user                     |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
# Stop all nodes under the local yashandb cluster
$ yasboot process yasdb stop -c yashandb

# Stop a single node on the local server
$ yasboot process yasdb stop -c yashandb --node-id 2-1 -f
```

## process yasdb start

This command is used to start the yasdb process on the local server.

|Option |Meaning |
| ------------------ | :------------------------------------------------------ |
| *-c, --cluster*      | The cluster name of YashanDB (mandatory parameter)                                |
| *-n, --node-id*      | Start a single node on the local server by specifying the ID of the target node (e.g., `1-2`. You can view the database information through the `yasboot cluster status` command and take the numeric string before the colon of `nodeid`)  |
| *-m, --start-mode*   | Startup mode, optional [mount|nomount|open], default is open   |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
# Start all nodes under the local yashandb cluster
$ yasboot process yasdb start -c yashandb

# Start a single node in the local cluster
$ yasboot process yasdb start -c yashandb --node-id 2-1 --start-mode open
```

## process yasdb restart

This command is used to restart the yasdb process on the local server.

|Option |Meaning |
| ------------------ | :------------------------------------------------------ |
| *-c, --cluster*      | The cluster name of YashanDB (mandatory parameter)                                |
| *-n, --node-id*      | Restart a single node on the local server by specifying the ID of the target node (e.g., `1-2`. You can view the database information through the `yasboot cluster status` command and take the numeric string before the colon of `nodeid`)  |
| *-m, --start-mode*   | Startup mode of the node, default is nomount                        |
| *-s, --stop-mode*    | Shutdown mode, optional [normal|immediate|abort], default is immediate (YAC/Distributed Cluster Deployment temporarily cannot be used)   |
| *-f, --force*        | Force stop the node, default is non-forced                      |
| *-u, --username*  | Specify the database user. If not specified, the default user sys is used |
| *-p, --password*  | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication) (enabled by default after installation) is activated, no password needs to be specified   |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
# Restart all nodes under the local yashandb cluster
$ yasboot process yasdb restart -c yashandb

# Restart a single node in the local yashandb cluster
$ yasboot process yasdb restart -c yashandb --node-id 2-1 --start-mode open
```

## process monit status

This command is used to check the status of the monit process.

|Option |Meaning |
| --------------- | :--------------- |
| *-c, --cluster*   | The cluster name of YashanDB (mandatory parameter) |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
$ yasboot process monit status -c yashandb
```

## process monit stop

This command is used to stop the monit process.

|Option |Meaning |
| --------------- | :--------------- |
| *-c, --cluster*   | The cluster name of YashanDB (mandatory parameter) |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
$ yasboot process monit stop -c yashandb
```

## process monit start

This command is used to start the monit process.

|Option |Meaning |
| --------------- | :--------------- |
| *-c, --cluster*   | The cluster name of YashanDB (mandatory parameter) |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
$ yasboot process monit start -c yashandb
```

## process monit restart

This command is used to restart the monit process.

|Option |Meaning |
| --------------- | :--------------- |
| *-c, --cluster*   | The cluster name of YashanDB (mandatory parameter) |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
$ yasboot process monit restart -c yashandb
```

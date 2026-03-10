## ycs status

This command can be used to check the status information of each node in ycs for the corresponding node (the result is equivalent to the `ycsctl status` command, and the number of nodes displayed is equal to the number of nodes deployed).

|Option |Meaning |
| --------------- | ------------------------------------------------------------ |
| *-c, --cluster*  | The name of the YashanDB cluster (mandatory)    |
| *-n, --node-id*  | Node ID (for example, 1-1, can be viewed through the `cluster status` command, without the colon and the numbers that follow) (mandatory) |

***Example***

```shell
$ yasboot ycs status -c yashandb -n 1-1
```

**Command Result**

```text

---------------------------------------------------------------------------------------------
Self Host ID|Cluster Master ID|YasFS Master ID|YasDB Master ID|Active Host Count
---------------------------------------------------------------------------------------------
1            1                 1               1               3
---------------------------------------------------------------------------------------------
Host ID   |Target    |State     |YasFS     |YasDB
---------------------------------------------------------------------------------------------
1          online     online     online     online
2          online     online     online     online
3          online     online     online     online
```

##  ycs show

This command can be used to view the configuration information of ycs for the corresponding node (the result is equivalent to the `ycsctl show config` command).

|Option |Meaning |
| --------------- | ------------------------------------------------------------ |
| *-c, --cluster*   | The name of the YashanDB cluster (mandatory)    |
| *-n, --node-id*   | Node ID (for example, 1-1, can be viewed through the `cluster status` command, without the colon and the numbers that follow) (mandatory) |

***Example***

```shell
$ yasboot ycs show -c yashandb -n 1-1
```

**Command Result**

```text

    Cluster name: yashandb, config version: 6
    Network timeout: 30s
    Disk heartbeat keep alive: 30s
    Default resource yasfs: enabled
    Shell in cluster:
      Start shell:   start.sh
      Stop shell:    stop.sh
      Monitor shell: monitor.sh
    Nodes in cluster:
      Node name: yas1, yascs/yasfs inter connect URL: 192.168.1.2:1788, Node ID: 1
        yasdb instance name:yasdb, yasdb instance id:1
      Node name: yas2, yascs/yasfs inter connect URL: 192.168.1.2:1789, Node ID: 2
        yasdb instance name:yasdb, yasdb instance id:1
      Node name: yas3, yascs/yasfs inter connect URL: 192.168.1.2:1790, Node ID: 3
        yasdb instance name:yasdb, yasdb instance id:1
```

## ycs instance start

This command is used to start the instance corresponding to the cluster node (the result is equivalent to the `ycsctl start instance` command, and the status of the instance is the same as that of the last started instance on the `yascs` process).

|Option |Meaning |
| --------------- | ------------------------------------------------------------ |
| *-c, --cluster*   | The name of the YashanDB cluster (mandatory)    |
| *-n, --node-id*   | Node ID (for example, 1-1, can be viewed through the `cluster status` command, without the colon and the numbers that follow) (mandatory) |
| *-w, --nowait*    | Do not wait for the execution result after running |
| *-d, --child*     | Display task and subtask information              |
| *--disable*       | Disable task progress bar display                  |
| *-u, --username*  | Specify the database user. If not specified, the default user sys is used |
| *-p, --password*  | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication) (enabled by default after installation) is activated, no password needs to be specified   |
| *--wait-timeout*  | Command execution timeout (hidden parameter)      |

***Example***

```shell
$ yasboot ycs instance start -c yashandb -n 1-1
```

## ycs instance stop

This command is used to stop the instance corresponding to the cluster node (the result is equivalent to the `ycsctl stop instance` command).

|Option |Meaning |
| --------------- | ------------------------------------------------------------ |
| *-c, --cluster*   | The name of the YashanDB cluster (mandatory)    |
| *-n, --node-id*   | Node ID (for example, 1-1, can be viewed through the `cluster status` command, without the colon and the numbers that follow) (mandatory) |
| *-f, --force*     | Whether to force stop the instance, default is not forced |
| *-w, --nowait*    | Do not wait for the execution result after running |
| *-d, --child*     | Display task and subtask information              |
| *--disable*       | Disable task progress bar display                  |
| *--wait-timeout*  | Command execution timeout (hidden parameter)      |

***Example***

```shell
$ yasboot ycs instance stop -c yashandb -n 1-1
```

##  ycs config

This command can be used to view the ycs configuration information for the corresponding node.

|Option |Meaning |
| --------------- | ----------------------------------------------------------- |
| *-c, --cluster*   | The name of the YashanDB cluster (mandatory)     |
| *-n, --node-id*   | Node ID (for example, 1-1, can be viewed through the `cluster status` command, without the colon and the numbers that follow) (mandatory) |
| *-q, --query*     | Query one or more parameters in the configuration (can use fuzzy matching) |
| *-a, -all*       | Query all parameters                              |

***Example***

```shell
$ ./bin/yasboot ycs config show -c yashandb -n 1-1
```

**Command Result**

```text

+--------------------------------------------------+
| parameters | current_value                       |
+--------------------------------------------------+
| _HOST_NAME | yas1                                |
+------------+-------------------------------------+
```

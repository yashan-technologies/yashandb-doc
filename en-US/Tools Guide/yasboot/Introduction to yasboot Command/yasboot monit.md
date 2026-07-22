## monit start

This command is used to start the monit process on all servers.

|Option |Meaning |
| --------------- | ------------------------ |
| *-c, --cluster*  | The cluster name of YashanDB (required parameter) |
| *-w, --nowait*   | Do not wait for the command result after execution |
| *-d, --child*    | Display task and subtasks information   |
| *--disable*      | Suppress the display of the task progress bar |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
$ yasboot monit start -c yashandb -d
```

## monit stop

This command is used to stop the monit process on all servers.

|Option |Meaning |
| --------------- | ------------------------ |
| *-c, --cluster*  | The cluster name of YashanDB (required parameter) |
| *-w, --nowait*   | Do not wait for the command result after execution |
| *-d, --child*    | Display task and subtasks information   |
| *--disable*      | Suppress the display of the task progress bar |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
$ yasboot monit stop -c yashandb -d
```

## monit restart

This command is used to restart the monit process on all servers.

|Option |Meaning |
| --------------- | ------------------------ |
| *-c, --cluster*  | The cluster name of YashanDB (required parameter) |
| *-w, --nowait*   | Do not wait for the command result after execution |
| *-d, --child*    | Display task and subtasks information   |
| *--disable*      | Suppress the display of the task progress bar |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
$ yasboot monit restart -c yashandb -d
```

## monit status

This command is used to view detailed information about the monit monitoring status.

|Option |Meaning |
| --------------- | ---------------------------------- |
| *-c, --cluster*  | The cluster name of YashanDB (required parameter) |
| *-n, --node-id* | The ID of the target node (e.g., `1-2`. You can view the database information through the `yasboot cluster status` command and take the numeric string before the colon of `nodeid`) |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
$ yasboot monit status -c yashandb
$ yasboot monit status -c yashandb -n 1-1
```

## monit summary

This command is used to view a brief overview of the monit monitoring status.

|Option |Meaning |
| --------------- | ---------------------------------- |
| *-c, --cluster*  | The cluster name of YashanDB (required parameter) |
| *-n, --node-id* | The ID of the target node (e.g., `1-2`. You can view the database information through the `yasboot cluster status` command and take the numeric string before the colon of `nodeid`) |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
$ yasboot monit summary -c yashandb
$ yasboot monit summary -c yashandb -n 1-1
```

## monit watch

This command is used to enable process monitoring for a specific node.

|Option |Meaning |
| --------------- | ---------------------------------- |
| *-c, --cluster*  | The cluster name of YashanDB (required parameter) |
| *-n,--node-id*   | Node ID (e.g., 1-1, can be viewed through the `yasboot cluster status` command, do not include the colon and subsequent numbers) (required parameter) |
| *-w, --nowait*   | Do not wait for the command result after execution |
| *-d, --child*    | Display task and subtasks information   |
| *--disable*      | Suppress the display of the task progress bar |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
$ yasboot monit watch -c yashandb -n 1-1 -d
```

## monit unwatch

This command is used to disable process monitoring for a specific node.

|Option |Meaning |
| --------------- | ---------------------------------- |
| *-c, --cluster*  | The cluster name of YashanDB (required parameter) |
| *-n,--node-id*   | Node ID (e.g., 1-1, can be viewed through the `yasboot cluster status` command, do not include the colon and subsequent numbers) (required parameter) |
| *-w, --nowait*   | Do not wait for the command result after execution |
| *-d, --child*    | Display task and subtasks information   |
| *--disable*      | Suppress the display of the task progress bar |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
$ yasboot monit unwatch -c yashandb -n 1-1 -d
```

>**Note**:
>
> When starting or restarting the database using the yasboot command, the monitor process is automatically enabled. To disable the monitor process for a specific node, manually execute the `yasboot monit unwatch` command.

## monit reload

This command is used to reload the configuration of monit on all servers.

|Option |Meaning |
| --------------- | ------------------------ |
| *-c, --cluster*  | The cluster name of YashanDB (required parameter) |
| *-w, --nowait*   | Do not wait for the command result after execution |
| *-d, --child*    | Display task and subtasks information   |
| *--disable*      | Suppress the display of the task progress bar |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
$ yasboot monit reload -c yashandb -d
```

## whitelist on

This command is used to enable the whitelist.

> **Warn**:
>
> When enabling the whitelist, if the current IP is empty, the IP of the current node will be written in.

|Option |Meaning |
| ---------------- | ---------------------------------------------------------- |
| *-c, --cluster*   | The cluster name of YashanDB                             |
| *-g, --group-id*  | The ID of the target group (e.g., `1`. You can view the database information via the `yasboot cluster status` command and take the number before the hyphen of `nodeid`)<br/> This option is mutually exclusive with the `-n, --node-id` option |
| *-n, --node-id*   | The ID of the target node (e.g., `1-2`. You can view the database information via the `yasboot cluster status` command and take the numeric string before the colon of `nodeid`)<br/> This option is mutually exclusive with the `-g, --group-id` option |
| *-d, --child*     | Display task and sub-task information                     |
| *--disable*       | Suppress the display of the task progress bar            |
| *-w, --nowait*    | Do not wait for the execution result after running the command |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
$ yasboot whitelist on -c yashandb -g 1
```

## whitelist off

This command is used to disable the whitelist.

|Option |Meaning |
| ---------------- | ---------------------------------------------------------- |
| *-c, --cluster*   | The cluster name of YashanDB                             |
| *-g, --group-id*  | The ID of the target group (e.g., `1`. You can view the database information via the `yasboot cluster status` command and take the number before the hyphen of `nodeid`)<br/> This option is mutually exclusive with the `-n, --node-id` option |
| *-n, --node-id*   | The ID of the target node (e.g., `1-2`. You can view the database information via the `yasboot cluster status` command and take the numeric string before the colon of `nodeid`)<br/> This option is mutually exclusive with the `-g, --group-id` option |
| *-d, --child*     | Display task and sub-task information                     |
| *--disable*       | Suppress the display of the task progress bar            |
| *-w, --nowait*    | Do not wait for the execution result after running the command |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
$ yasboot whitelist off -c yashandb -g 1
```

## whitelist add

This command is used to add IPs to the whitelist.

|Option |Meaning |
| ---------------- | ---------------------------------------------------------- |
| *-c, --cluster*   | The cluster name of YashanDB                             |
| *-g, --group-id*  | The ID of the target group (e.g., `1`. You can view the database information via the `yasboot cluster status` command and take the number before the hyphen of `nodeid`)<br/> This option is mutually exclusive with the `-n, --node-id` option |
| *-n, --node-id*   | The ID of the target node (e.g., `1-2`. You can view the database information via the `yasboot cluster status` command and take the numeric string before the colon of `nodeid`)<br/> This option is mutually exclusive with the `-g, --group-id` option |
| *--ip*            | The IPs to be added, supports multiple IPs separated by commas |
| *-r, --replace*   | Replace all current IPs in the whitelist with the specified IPs |
| *-d, --child*     | Display task and sub-task information                     |
| *--disable*       | Suppress the display of the task progress bar            |
| *-w, --nowait*    | Do not wait for the execution result after running the command |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
# IPv4
$ yasboot whitelist add -c yashandb -n 1-1 --ip 192.168.1.1,192.168.1.2

# IPv6, no brackets needed
$ yasboot whitelist add -c yashandb -n 1-1 --ip fc00:7::126,fe80::20c:29ff:fea5:66c2%ens33
```

## whitelist remove

This command is used to remove IPs from the whitelist.

|Option |Meaning |
| ---------------- | ---------------------------------------------------------- |
| *-c, --cluster*   | The cluster name of YashanDB                             |
| *-g, --group-id*  | The ID of the target group (e.g., `1`. You can view the database information via the `yasboot cluster status` command and take the number before the hyphen of `nodeid`)<br/> This option is mutually exclusive with the `-n, --node-id` option |
| *-n, --node-id*   | The ID of the target node (e.g., `1-2`. You can view the database information via the `yasboot cluster status` command and take the numeric string before the colon of `nodeid`)<br/> This option is mutually exclusive with the `-g, --group-id` option |
| *--ip*            | The IPs to be removed, supports multiple IPs separated by commas |
| *-a, --all*       | Clear all IPs, taking precedence over --ip               |
| *-d, --child*     | Display task and sub-task information                     |
| *--disable*       | Suppress the display of the task progress bar            |
| *-w, --nowait*    | Do not wait for the execution result after running the command |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
$ yasboot whitelist remove -c yashandb -n 1-1 -a
```

## whitelist show

This command is used to view the details of the whitelist.

|Option |Meaning |
| ---------------- | ---------------------------------------------------------- |
| *-c, --cluster*   | The cluster name of YashanDB                             |
| *-g, --group-id*  | The ID of the target group (e.g., `1`. You can view the database information via the `yasboot cluster status` command and take the number before the hyphen of `nodeid`)<br/> This option is mutually exclusive with the `-n, --node-id` option |
| *-n, --node-id*   | The ID of the target node (e.g., `1-2`. You can view the database information via the `yasboot cluster status` command and take the numeric string before the colon of `nodeid`)<br/> This option is mutually exclusive with the `-g, --group-id` option |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
$ yasboot whitelist show -c yashandb -g 2
node 2-1:
	TCP.VALIDNODE_CHECKING = YES
	TCP.INVITED_NODES = 127.0.0.1

node 2-2:
	TCP.VALIDNODE_CHECKING = YES
	TCP.INVITED_NODES = 127.0.0.1
```

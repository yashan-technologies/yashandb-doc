## config node gen

This command is used to generate the configuration files related to expansion: hosts_add.toml and ${cluster}_add.toml.

|Option |Meaning |
| -------------------- | --------------------- |
| *-c, --cluster*       | The name of the cluster to be generated (required parameter) |
| *-u, --username*      | SSH username for the server                                   |
| *--groupname*         | User group name, defaults to the same as `username`          |
| *-p, --password*      | SSH login password                                           |
| *-N, --no-password*    | SSH passwordless login                                        |
| *--ip*                | IP addresses for deployment, separated by commas `,`         |
| *--port*              | Server SSH connection port                                    |
| *-i, --install-path*  | Database installation path (HOME directory)                  |
| *--data-path*         | DATA directory of the database instance                       |
| *-f, --force*          | Whether to force database deployment, forcing means that the current server running status will not be checked |
| *-g, --group-id*       | Group ID (can be seen via the cluster status command, the value before the hyphen in nodeid is the group ID, e.g., `1-1:1` has a group ID of 1), defaults to 1 |
| *--node*              | Total number of new nodes. Defaults to 1                     |
| *--host-id*           | ID of the deployed server, multiple servers separated by commas (deprecated parameter) |
| *--host-ids*           | IDs of the deployed servers, separated by commas              |
| *--begin-port*        | Starting port 1688                                           |
| *--sys-password*      | Password for the database sys user                           |
| *-su,--sudo-username* | SSH user with sudo privilege, defaults to the same value as --username (used to execute commands that require sudo privilege, such as creating cgroup directories) |
| *-sp,--sudo-password* | Password for the SSH user with sudo privilege, defaults to the same value as --password |
| *--no-add-yasdba*       | Do not add the installation user to the YASDBA user group    |
| *--ssl-protocol*      | SSL protocol, only supports SSL                               |
| *--ssl-path*          | Directory where SSL keys are located                        |
| *--log-path*          | Paths for yasagent, yasom, database run.log, and slow.log    |
| *-m, --mode*          | Syntax mode, optional values are [yashan, mysql], defaults to yashan, applicable only for Standalone Deployment |
| *--vips*              | When expanding YACs, if the original cluster has configured VIP, you need to specify the VIP configuration information list for expansion instances through this parameter (format: IP address/subnet mask/network card name, e.g., 192.168.60.4/255.255.255.0/ens192), the number of VIPs must match the number of expansion nodes, and multiple VIP configuration information separated by commas `,` |

***Example***1 for expanding nodes on new servers, generating hosts_add.toml and ${cluster}_add.toml files

```shell
$ yasboot config node gen -c yashandb -u yashan -p password --ip ip1,ip2...ipn --port 22  --install-path /var/database/yashan -g 1 --node 2 --vips 192.168.60.4/24/ens192,192.168.60.5/24/ens192
```

> **Note**:
>
> If the --ssl-path and --ssl-protocol parameters are used during deployment, the configuration information generated during expansion must also use these 2 parameters, otherwise, it will lead to expansion failure.

***Example***2 for expanding nodes on existing servers, generating ${cluster}_add.toml

```shell
$ yasboot config node gen -c yashandb --host-ids host0001,host0002 -g 1 --node 2
```

## config host gen

This command is used to generate the configuration file yascheck.toml for server inspection.

|Option |Meaning |
| -------------- | ---------------------------------- |
| *-o,--output*    | Path for generating the configuration file (default is the current directory) |
| *--ip*           | This IP will be used as the IP address for network testing |

***Example***

```bash
$ yasboot config host gen -o ./ --ip 192.168.x.x
```

## config sql gen

This command is used for collecting SQL information with the configuration file sqlcollect.toml.

|Option |Meaning |
| -------------- | ---------------------------------- |
| *-o,--output*    | Path for generating the configuration file (default is the current directory) |

***Example***

```bash
$ yasboot config sql gen -o ./
```

## config patrol gen

This command is used to generate inspection strategy configuration files based on the input options.

|Option |Meaning |
| ----------------------- | --------------------- |
| *-c, --cluster*         | Name of the YashanDB cluster (required parameter)        |
| *-s, --strategy-name*   | Name of the inspection strategy (required parameter)      |
| *-t, --strategy-type*   | Inspection strategy type:<br/>\* PERIOD: periodic strategy, can be executed multiple times periodically after application, default<br/>\* TIMING: timed inspection strategy, executed once at a set time after application |
| *-ce,--cron-expression*  | Cron expression used to set inspection time, consisting of five fields: minutes, hours, day, month, week |
| *-f,--frequency*        | Inspection frequency:<br/>\* monthly: every month<br/>\* weekly: every week, default<br/>\* daily: every day<br/>\* hourly: every hour |
| *-d,--days*             | Specific execution days for inspection                    |
| *-st, --start-time*     | Specific start time for inspection                        |
| *-sp,--store-path*      | Path for storing inspection files                         |
| *-sd,--store-days*      | Maximum retention days for inspection files, defaults to saving all                         |
| *-sn,--store-num*       | Maximum number of retained inspection files, defaults to saving all                         |
| *--yascheck-toml*      | yascheck.toml configuration file                          |
| *--sql-toml*           | sqlcollect.toml configuration file                        |
| *--patrol-module*       | Inspection modules, optional `host`, `sql`, and `gstack`, defaults to collecting all modules, use commas `,` to input multiple values |
| *-F,--format*           | Format of the inspection results, options are json and html                                   |
| *--node-ids*            | SQL collection node IDs, separated by commas `,`, defaults to `1-1` (can use `all` to indicate collection of all nodes) |
| *-o,--output*           | Path for saving the generated configuration file, defaults to the current path |

```shell
# Setting inspection time using the `--cron-expression` parameter and generating configuration files
$ yasboot config patrol gen -c yashandb -s ps01  --cron-expression "10 12 ? 1,5,6,0 *" 

# Setting inspection time using `--frequency, --days, --start-time` parameters and generating configuration files
$ yasboot config patrol gen -c yashandb -s ps01  -f weekly -d 1,5-7  -st 12:10

# Generating configuration files for inspection modules host and gstack
$ yasboot config patrol gen -c yashandb -s ps01  --cron-expression "10 12 ? 1,5,6,0 *" --patrol-module host,gstack
```

## config group gen

This command is used to generate the configuration files related to expansion: hosts_add.toml and ${cluster}_add.toml.

|Option |Meaning |
| -------------------- |------|
| *-c, --cluster*       | The name of the cluster to be generated (required parameter) |
| *-u, --username*      | SSH username for the server                                   |
| *--groupname*         | SSH user group name                                          |
| *-p, --password*      | SSH login password                                           |
| *-N, --no-password*    | SSH passwordless login                                        |
| *--ip*                | IP addresses for deployment, separated by commas `,`         |
| *--port*              | Server SSH connection port                                    |
| *-i, --install-path*  | Database installation path (HOME directory)                  |
| *--host-id*           | ID of the deployed server, multiple IDs separated by commas (deprecated parameter) |
| *--host-ids*          | IDs of the deployed servers, separated by commas              |
| *--data-path*         | DATA directory of the database instance                       |
| *-f, --force*          | Whether to force database deployment, forcing means that the current server running status will not be checked |
| *--node*              | Number of nodes in the new group, defaults to 1              |
| *--group*             | Number of new node groups, defaults to 1                      |
| *--begin-port*        | Starting port 1688                                          |
| *--sys-password*      | Password for the database sys user                           |
| *-su,--sudo-username* | SSH user with sudo privilege, defaults to the same value as --username (used to execute commands that require sudo privilege, such as creating cgroup directories) |
| *-sp,--sudo-password* | Password for the SSH user with sudo privilege, defaults to the same value as --password |
| *--no-add-yasdba*       | Do not add the installation user to the YASDBA user group    |
| *--ssl-protocol*      | SSL protocol, only supports SSL                               |
| *--ssl-path*          | Directory where SSL keys are located                          |
| *--log-path*          | Paths for yasagent, yasom, database run.log, and slow.log    |
| *-t, --type*          | Type of the new node group. Defaults to DN (hidden parameter) |

***Example***1 for expanding nodes on new servers, generating hosts_add.toml and ${cluster}_add.toml files

```shell
$ yasboot config group gen -c yashandb -u yashan -p password --ip ip1,ip2...ipn --port 22  --install-path /var/database/yashan --group 2 --node 3
```

> **Note**:
>
> If the --ssl-path and --ssl-protocol parameters are used during deployment, the configuration information generated during expansion must also use these 2 parameters, otherwise, it will lead to expansion failure.

***Example***2 for expanding nodes on existing servers, generating ${cluster}_add.toml

```shell
$ yasboot config group gen -c yashandb --host-ids host0001,host0002 --group 2 --node 3
```

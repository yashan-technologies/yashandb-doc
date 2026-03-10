## cluster deploy
<span id="deploy" name="deploy" class="yaslink"></span>

This command is used to deploy and initialize YashanDB on all servers according to the configuration file.

|Option |Meaning |
| -------------------- | ---------------------------------------- |
| *-w, --nowait*       | Do not wait for the execution command result after running |
| *-t, --toml*         | The path of the toml file for deploying the database (required) |
| *-d, --child*        | Display information about tasks and subtasks  |
| *--disable*          | Disable the display of the task progress bar   |
| *--dump-uuid*        | Write the task UUID file to the specified folder |
| *-p, --sys-password* | Set the password for the database sys user, with the following requirements:<br/>* Password length must be 8 to 64 characters<br/>* Password must not contain the corresponding database username<br/>* Password must include numbers, letters, and special characters<br/>* Special characters related to OS commands (e.g., `@`, `/`, `.`, `!`, `$`, `'`, etc.) must be escaped |
| *--yfs-force-create* | Force create diskgroup for yfs (only applicable to YAC/Distributed Cluster) |
| *--recommend-force*  | Skip memory over-allocation checks            |
| *--wait-timeout*     | Command execution timeout (hidden parameter)   |
| *--ignore-hostname*   | Ignore server name (hidden parameter) <br/>* When not specified, the server name is used as the hostid for each node in the database by default<br/>* When this option is specified, *yasboot* will automatically generate default strings (yas1, yas2...) as hostid<br/>This option should **only** be used when instances in a YAC/Distributed Cluster deployment reside on servers with identical names that cannot be modified. **Not recommended** for other scenarios.    |

***Example***

```shell
$ yasboot cluster deploy -t yashandb.toml -p sys_password
```

## cluster status

This command is used to query the status of YashanDB processes on all servers.

|Option |Meaning |
| --------------- | -------------------------------- |
| *-c, --cluster*   | The cluster name of YashanDB (required) |
| *-d, --detail*    | Display more details              |
| *-b, --by*        | Display indexed by host or group, default is host |
| *-u, --username*  | Specify the database user. If not specified, the default user sys is used |
| *-p, --password*  | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证) (enabled by default after installation) is activated, no password needs to be specified    |

***Example***

```shell
$ yasboot cluster status -c yashandb -d
$ yasboot cluster status -b group -c yashandb -d
```

## cluster clean

This command is used to clean YashanDB on all servers and can also be used for environment cleanup prior to database recovery.

> **Warn**:
>
> If --restore is not specified, the clean operation will stop all database processes on all servers (using --purge will also delete data) and remove the database cluster from yasom management, meaning yasom can no longer manage this database cluster.

|Option |Meaning |
| --------------- | ------------------------------------------- |
| *-c, --cluster*   | The cluster name of YashanDB (required) |
| *-f,--force*      | Force clean flag; a confirmation prompt will appear if this parameter is not used |
| *-r,--restore*    | Pre-execution preparation for restore; delete all content in each node's/dbfiles, /local_fs directories, and start the cluster in nomount mode |
| *-w, --nowait*    | Do not wait for the execution command result after running |
| *--purge*         | Clean the cluster and delete all node data, default is false |
| *--with-arch*     | Delete archive log files (effective when --restore is true), default is false |
| *-d, --child*     | Display information about tasks and subtasks |
| *--disable*       | Disable the display of the task progress bar |
| *-p, --password*  | Password for the database `sys` user; effective when --restore is true<br/>If [OS authentication](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证) (enabled by default after installation) is activated, no password needs to be specified |
| *--wait-timeout*  | Command execution timeout (hidden parameter) |

***Example***

```shell
$ yasboot cluster clean -c yashandb --purge
# For full recovery, if you want to restore as much data as possible, do not delete archives by default
$ yasboot cluster clean -c yashandb --restore
# For full recovery, if only recovering to the backup snapshot timestamp, delete archives
$ yasboot cluster clean -c yashandb --restore --with-arch
```

## cluster stop

This command is used to stop YashanDB services on all servers.

|Option |Meaning |
| ----------------- | ----------------------- |
| *-c, --cluster*     | The cluster name of YashanDB (required) |
| *-f, --force*       | Force stop flag (kill -9), default is false |
| *-w, --nowait*      | Do not wait for the execution command result after running |
| *-d, --child*       | Display information about tasks and subtasks |
| *--disable*         | Disable the display of the task progress bar |
| *-s, --stop-mode*   | Database shutdown method, options [normal&#124;immediate&#124;abort], default is immediate (YAC/Distributed Cluster Deployment cannot use this temporarily) |
| *-u, --username*    | Specify the database user (defaults to sys user if not specified) |
| *-p, --password*    | Password for the database user |
| *--wait-timeout*    | Command execution timeout (hidden parameter) |

***Example***

```shell
$ yasboot cluster stop -c yashandb
$ yasboot cluster stop -c yashandb -f
```

## cluster start

This command is used to start YashanDB services on all servers.

|Option |Meaning |
| ------------------ | ---------------------------------------------------- |
| *-c, --cluster*      | The cluster name of YashanDB (required) |
| *-m, --start-mode*   | The mode for starting the database instance, options [open&#124;nomount&#124;mount], default is open (YAC/Distributed Cluster Deployment cannot start in mount mode temporarily) |
| *-w, --nowait*       | Do not wait for the execution command result after running |
| *-d, --child*        | Display information about tasks and subtasks |
| *--disable*          | Disable the display of the task progress bar |
| *-u, --username*  | Specify the database user. If not specified, the default user sys is used |
| *-p, --password*  | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证) (enabled by default after installation) is activated, no password needs to be specified   |
| *--wait-timeout*     | Command execution timeout (hidden parameter) |
| *--wallet-password*  | Database wallet password |

***Example***

```shell
$ yasboot cluster start -c yashandb
$ yasboot cluster start -c yashandb -m nomount
```

## cluster restart

This command is used to restart YashanDB services on all servers.

|Option |Meaning |
| ------------------ | ----------------------- |
| *-c, --cluster*      | The cluster name of YashanDB (required) |
| *-m, --start-mode*   | The mode for starting the database instance, options [open&#124;nomount&#124;mount], default is open (YAC/Distributed Cluster Deployment cannot start in mount mode temporarily) |
| *-w, --nowait*       | Do not wait for the execution command result after running |
| *-d, --child*        | Display information about tasks and subtasks |
| *-f, --force*        | Force stop flag (kill -9), default is false |
| *--disable*          | Disable the display of the task progress bar |
| *-s, --stop-mode*    | Database shutdown method, options [normal&#124;immediate&#124;abort], default is immediate (YAC/Distributed Cluster Deployment cannot use this temporarily) |
| *-u, --username*     | Specify the database user (defaults to sys user if not specified) |
| *-p, --password*     | Password for the database user |
| *--wait-timeout*     | Command execution timeout (hidden parameter) |
| *--wallet-password*  | Database wallet password |

***Example***

```shell
$ yasboot cluster restart -c yashandb
$ yasboot cluster restart -c yashandb -m nomount -f
```

## cluster password set

This command is used to batch set sys account passwords for all databases in the cluster.

|Option |Meaning |
| -------------------- | --------------------------------------------------------- |
| *-c, --cluster*        | The cluster name of YashanDB (required) |
| *-o, --old-password*   | Old sys password; required if the cluster is not managed in yasom |
| *-n, --new-password*   | New sys password (required), must comply with [password complexity](../../../产品安全/身份标识与鉴别/密码认证/密码策略.html#password_complexity) and [password policy](../../../产品安全/身份标识与鉴别/密码认证/密码策略) requirements<br/>If the password contains special characters related to OS commands (e.g., `@`, `/`, `.`, `!`, `$`, `'`, etc.), they must be escaped |
| *-w, --nowait*         | Do not wait for the execution command result after running |
| *-d, --child*          | Display information about tasks and subtasks |
| *--disable*            | Disable the display of the task progress bar |
| *-m,--hash-method*     | Password encryption algorithm, options [SHA256,SM3] |

***Example***

```shell
$ yasboot cluster password set -n newpasswd -c yashandb
```

## cluster log

This command is used to collect all log information and the database configuration file from all nodes in the database cluster.

|Option |Meaning |
| ------------------ | ---------------------------------------------- |
| *-c, --cluster*      | The cluster name of YashanDB (required)      |
| *-s,--start*         | Start time for collecting logs, default is the current day's midnight |
| *-e, --end*          | End time for collecting logs, default is the current time |
| *-o, --output*       | Log collection download path (effective when --no-download is false) |
| *-n,--no-download*   | Collect logs but do not download to local, default is false |
| *-f, --force*        | No confirmation needed, default is false      |
| *--disable*          | Disable the display of the task progress bar   |

***Example***

```shell
$ yasboot cluster log -c yashandb -n
# Specify start and end time
$ yasboot cluster log -c yashandb -n -s '2006-01-02 15:04:05' -e '2006-01-02 15:04:05'
```

## cluster join

This command is used to manage the database cluster using yasom based on the configuration file.

|Option |Meaning |
| -------------- | ------------------------ |
| *-t, --type*      | The cluster type of YashanDB   |
| *-c, --config*    | Management configuration file   |
| *-f, --force*     | No confirmation needed; default is false |
| *-w, --nowait*    | Do not wait for the execution command result after running |
| *-d, --child*     | Display information about tasks and subtasks |
| *--disable*       | Disable the display of the task progress bar |

***Example***

```shell
$ yasboot cluster join -t SE --config join_demo.toml
```

## cluster upgrade

This command is used to upgrade the version of the database on all nodes in the cluster.

|Option |Meaning |
| --------------- | ----------------------------------------- |
| *-c, --cluster*   | The cluster name of YashanDB (required)   |
| *-f, --force*     | Force upgrade, ignoring pre-upgrade checks, default is false |
| *-u, --username*  | Specifies the database user; if not specified, the default user `sys` is used |
| *-p, --password*  | Password for the database user              |
| *--backup-reserved-space*   | Reserved capacity for the temporary backup path during offline upgrade, units can be B/KB/MB/GB/TB/PB/EB (B can be omitted, e.g., 2GB or 2G), default is 5G<br>Cannot be used simultaneously with the `--rolling` parameter  |
| *--backup-path* | During offline upgrade, a custom database backup path can be specified<br/>If this parameter is left empty or not specified, the system will automatically create a temporary directory (${old_version_YASDB_HOME}/upgrade_tmp/backup) as the backup directory<br>Cannot be used simultaneously with the `--rolling` parameter |
| *--disk-found-path*| YAC/Distributed Cluster disk discovery path (default is /dev/yfs) |
| *--system-data*   | YAC/Distributed Cluster system data disks, supports multiple inputs, separated by commas |
| *--disk-config*              | The disk information required to upgrade YAC from version 23.2.x.x and earlier to version 23.4.x.x and above, refer to [YAC upgrade configuration file](../配置文件/共享集群升级配置文件) |
| *--rolling*                  | Whether to perform a rolling upgrade                         |
| *--keep-primary*             | Retain the identity of the original primary node after rolling upgrade<br />When this parameter is specified, two primary-standby switches will occur during the incompatible version rolling upgrade. |
| *--logic-stdby-sync-timeout* | In incompatible version rolling upgrade scenarios, the timeout for waiting the logical standby database to synchronize, in seconds. Default is 600 |
| *--logic-stdby-file-size* | In incompatible version rolling upgrade scenarios for Primary/Standby YAC, each instance in the primary cluster will automatically create 3 STANDBY LOG files to store redo logs from the logical standby during the upgrade<br />This parameter specifies the total size of the 3 STANDBY LOG files on the primary cluster's master instance (other instances always use the minimum value). The default is 2048M, and the value must not exceed 10G |
| *--rolling-sync-timeout* | In incompatible version rolling upgrade scenarios for Primary/Standby YAC, the default timeout for the upgrade task is 2 hours. If exceeded, the upgrade will be interrupted |
| *--continue*                 | In scenarios where rolling upgrade between incompatible versions fails and prompts "please execute 'yasboot cluster upgrade --rolling --continue' to continue upgrade", you can specify this parameter to resume the upgrade |
| *-w, --nowait*               | Do not wait for the execution command result after running   |
| *-d, --child*                | Display information about tasks and subtasks                 |
| *--disable*                  | Disable the display of the task progress bar                 |
| *--wait-timeout*             | Command execution timeout (hidden parameter)                 |

***Example***

```shell
$ yasboot cluster upgrade --cluster yashandb
```

## cluster rollback

This command is used to roll back the database version on all nodes in the cluster.

|Option |Meaning |
| --------------- | ---------------------------- |
| *-c, --cluster*   | The cluster name of YashanDB (required) |
| *-w, --nowait*    | Do not wait for the execution command result after running |
| *-d, --child*     | Display information about tasks and subtasks |
| *--disable*       | Disable the display of the task progress bar |
| *-f, --force*     | Force rollback when the database is not running |
| *--rolling*       | Whether to perform a rolling version rollback; cannot be used with force |
| *-u, --username*  | Specify the database user. If not specified, the default user sys is used |
| *-p, --password*  | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证) (enabled by default after installation) is activated, no password needs to be specified   |
| *--wait-timeout*  | Command execution timeout (hidden parameter) |

***Example***

```shell
$ yasboot cluster rollback -c yashandb
```

## cluster config show

This command is used to display the parameter configuration of the database cluster. Unlike other config show commands, cluster config show does not support querying all configuration parameters.

|Option |Meaning |
| --------------- | ------------------------------------------ |
| *-c, --cluster*   | The cluster name, a unique identifier (required) |
| *-q, --query*     | Parameter name, which can be a fuzzy parameter name (required) |
| *-u, --username*  | Specify the database user. If not specified, the default user sys is used |
| *-p, --password*  | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证) (enabled by default after installation) is activated, no password needs to be specified   |

Supports fuzzy matching, as well as exact matching.

```shell
$ yasboot cluster config show -c yashandb -q ELECTION
+-------------------------+
| node   | HA_ELECTION_TIMEOUT | HA_ELECTION_ENABLED | HA_ELECTION_LEADER_LEASE_ENABLED |
+-------------------------+
| dn-5-1 | 9                   | FALSE               | FALSE                            |
+--------+---------------------+---------------------+----------------------------------+
| mn-1-1 | 9                   | FALSE               | FALSE                            |
+--------+---------------------+---------------------+----------------------------------+
| dn-3-1 | 9                   | FALSE               | FALSE                            |
+--------+---------------------+---------------------+----------------------------------+
| dn-4-1 | 9                   | FALSE               | FALSE                            |
+--------+---------------------+---------------------+----------------------------------+
| cn-2-1 | 9                   | FALSE               | FALSE                            |
+--------+---------------------+---------------------+----------------------------------+
```

## cluster config set

This command is used to modify cluster-level configuration parameters.

|Option |Meaning |
| ---------------- | ------------------------------------ |
| *-c, --cluster*   | The cluster name (required)            |
| *-k, --key*       | The name of the configuration parameter to be modified (required); Standalone Deployment modification supports PROTECTION_MODE |
| *-v, --value*     | The target value of the modified parameter (required) |
| *-r, --rollback*  | Supports rolling back cluster parameters (mutually exclusive with force parameter) |
| *-s, --scope*     | Supports setting the parameter's scope  |
| *-f, --force*     | Supports forced modification of parameters (even if node status is abnormal) |
| *-w, --nowait*    | Do not wait for the execution command result after running |
| *-d, --child*     | Display information about tasks and subtasks |
| *--disable*       | Disable the display of the task progress bar |
| *-u, --username*  | Specify the database user. If not specified, the default user sys is used |
| *-p, --password*  | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证) (enabled by default after installation) is activated, no password needs to be specified   |

Modify cluster parameters.

```shell
$ yasboot cluster config set -c yashandb -k REDO_BUFFER_SIZE -v 20M
```

Force modify cluster parameters.

```shell
$ yasboot cluster config set -c yashandb -k REDO_BUFFER_SIZE -v 20M -f
```

Rollback parameters after modification failure.

```shell
$ yasboot cluster config set -c yashandb -k HA_HEARTBEAT_INTERVAL -v 500 -r
```

## cluster config unset

This command is used to remove previously set configuration parameters.

|Option |Meaning |
| --------------- | -------------------------------- |
| *-c, --cluster*   | The cluster name (required)           |
| *-k, --key*       | The name of the configuration parameter to be modified (required) |
| *-w, --nowait*    | Do not wait for the execution command result after running |
| *-d, --child*     | Display information about tasks and subtasks |
| *--disable*       | Disable the display of the task progress bar |
| *-u, --username*  | Specify the database user. If not specified, the default user sys is used |
| *-p, --password*  | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证) (enabled by default after installation) is activated, no password needs to be specified   |

***Example***

```shell
$ yasboot cluster config unset -c yashandb -k HA_ELECTION_ENABLED
```

## cluster esn gen

This command is used to generate esn information based on the servers used.

|Option |Meaning |
| --------------- | ---------------------------------------|
| *-c, --cluster*   | The cluster name, a unique identifier (required) |
| *-i, --info*      | Server information, usually not required; the tool can automatically retrieve it |
| *-v, --version*   | esn version information, default is v1   |

```shell
$ yasboot cluster esn gen -c yashandb
```

## cluster license update

This command is used to update the database license.

|Option |Meaning |
| ------------------ | ---------------------------------------------------- |
| *-c, --cluster*      | The cluster name of YashanDB (required) |
| *-w, --nowait*       | Do not wait for the execution command result after running |
| *-d, --child*        | Display information about tasks and subtasks |
| *--disable*          | Disable the display of the task progress bar |
| *-u, --username*  | Specify the database user. If not specified, the default user sys is used |
| *-p, --password*  | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证) (enabled by default after installation) is activated, no password needs to be specified   |
| *--wait-timeout*     | Command execution timeout (hidden parameter) |
| *-f, --file*         | License file path (it is recommended to use an absolute path) |

```shell
$ yasboot cluster license update -c yashandb -f /var/database/license.lic
```

## cluster load

This command is used to reload database information into yasom.

It is recommended not to specify the -t parameter when executing this command for the first time; a [database topology file](../Configuration Files/Database Topology File) will be generated in the current directory to record the database topology information. If, after the first load, the database information does not match the actual information, the [database topology file](../Configuration Files/Database Topology File) must be corrected and the -t parameter specified to load again.

|Option |Meaning |
| --------------- | -------------------------------- |
| *-c, --cluster*   | The cluster name (required)         |
| *-t,--toml*       | Load database information from the [database topology file](../Configuration Files/Database Topology File). |
| *-f,--force*      | Directly refresh data to yasom without a second confirmation |
| *-u, --username*  | Specify the database user. If not specified, the default user sys is used |
| *-p, --password*  | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证) (enabled by default after installation) is activated, no password needs to be specified   |

***Example***

```shell
$  yasboot cluster load -c yashandb

$  yasboot cluster load -c yashandb -t yashandb_topology.toml
```

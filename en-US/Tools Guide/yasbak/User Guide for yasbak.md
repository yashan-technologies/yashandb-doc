## Command Introduction

*yasbak* is a management tool for *yasrman*, providing a simple wrapper around *yasrman* and retaining some necessary parameters during execution. Compared to *yasrman*, using *yasbak* for backup and restore can reduce the number of parameter inputs and simplify the complexity of the backup and restore processes.

## View Help

Use the command `yasbak -h` to view the help information.

```shell
$ yasbak -h
```

## User Guide

### yasbak deploy

This command is used to initialize the runtime environment for *yasbak* and *yasrman*.

When this command is executed, it will check if it can connect to the database; if the connection fails, the initialization will fail.

|Option |Meaning |
| --------------- | ------------------------------------------------------------------------------------------------------ |
| *-c,--cluster*  | Specify a name that will be used for configuring the database naming; one database corresponds to one name, recommended to keep consistent with the name used during *yasboot* deployment         |
| *-a,--addr*     | Specify the yasom access address for the database; *yasbak* will communicate with yasom through this address                                              |
| *-k,--key*      | Token used for validation when connecting to yasom, needs to match the configuration in yasom. The token value is configured in the installation directory of ​​yasom​​, located at the path `${YASDB_HOME}/om/<Cluster_name>/conf/tls.toml`.              |
| *-D,--cata-log* | The cata log used by yasrman; the specified path will create this directory. If not specified, the default cata log path will follow the same rule as *yasrman*. If a relative path is provided, the directory will be created under the current working directory as the cata log path. |
| *-u,--user*     | Username used to connect to the database; this user will be used by default for subsequent backup executions                           |
| *-p,--password* | Password corresponding to the database user; this password will be stored in the local configuration file after multiple encryptions, and can be cleared by executing clean            |
| *-t,--cert*     | When TLS encryption communication is specified for yasom, the corresponding encryption certificate needs to be specified                                                          |
| *-S,--server*   | When a server name is specified for yasom, this name needs to be specified                                                                   |
| *-f,--force*    | Force the initialization of the runtime environment; when this parameter is specified, no privilege checks will be conducted                                         |

***Example***

```shell
$ yasbak deploy -c yashandb -a 127.0.0.1:1675 -k 96ed7a2c90e81a9e -D ./catalog -u sys -p sys_password
```

### yasbak run

This command is used to execute backup, restore, clean backup, and other statements for *yasrman*.

|Option |Meaning |
| ---------------- | ------------------------------------------------------------ |
| *-c,--cluster*    | The cluster name specified during deployment                    |
| *-s,--sql*        | Specify the SQL statement to run with *yasrman*; for SQL usage, please refer to [yasrman User Guide](../yasrman/User Guide for yasrman/00User Guide for yasrman) |
| *-u,--user*       | The user used to execute *yasrman*; if not specified, the username from deployment will be used      |
| *-p,--password*   | The password used for executing *yasrman*; if not specified, the password from deployment will be used, and *yasbak* will decrypt it for internal use |
| *-r,--role*       | Optional parameter: primary, standby; use the specified type of node for backup operations   |
| *-a, --addr*      | The connection address for yasdb; if this parameter value is provided, this node will be specified, and --role will be ineffective |
| *-b,--build-all*  | When restoring backups, restore other standby nodes; this parameter can also be used together with --skip-validate and --overwrite |
| *--skip-validate* | Must be specified before -b for this parameter to take effect; this parameter indicates to skip the pre-check when establishing the standby database, the default is to check |
| *--overwrite*     | Must be specified before -b for this parameter to take effect; this parameter indicates to overwrite files with the same name during creating standby databases (REDO files and archived files will not be overwritten, manual inspection and clean-up are required), the default is not to overwrite |

> **Note**: 
>
> When both addr and role are not specified, the node selection rules are as follows:
>
> -  Standalone Deployment: During backup, select the standby node to perform the operation (directly choose this node if there is only one node); during recovery, select the first node in the node list to perform the operation.
> -  ISC Distributed Cluster Deployment: Select the first CN to perform backup or recovery.
> -  YAC Deployment: Select the first node for backup, and select the master role node for recovery.

***Example***

*yasrman* provides an interface for integrating with third-party XBSA APIs, requiring the addition of the PARAMS parameter in the SQL statement; refer to [yasrman params Introduction](../yasrman/User Guide for yasrman/00User Guide for yasrman) for PARAMS details.

```shell
# Execute backup
YASRMAN_PARAM="XBSA_LIBRARY=/lib/libxbsa.so, TOKEN=157257815837, ENV=(key1=val1,key2=val2)"

$ yasbak run -c yashandb -s "backup database tag 'full_01' parallelism 4 dest client params '${YASRMAN_PARAM}'"
 
# Restore backup
$ yasbak run -c yashandb -s "restore database from tag 'full_01' dest client params '${YASRMAN_PARAM}'" -u sys -p sys_password  --build-all
 
# Clean up backup
$ yasbak run -c yashandb -s "delete backupset tag 'full_01'"
```

### yasbak reset

This command is used to clean up the corresponding database data and start the database in nomount mode, suitable for recovering the database from a backup set.

|Option |Meaning |
| -------------- | ---------------------------- |
| *-c,--cluster*   | The cluster name specified during deployment    |
| *-f,--force*     | Ignore confirmation, directly clean            |
| *-p,--password*   | sys user password                 |
| *--with-arch*    | Delete archive log files                  |
| *-d, --child*    | Show information about tasks and subtasks       |
| *--disable*      | Suppress showing task progress display           |
| *-w, --nowait*   | Do not wait for command execution results after running   |

***Example***

```shell
$ yasbak reset -c yashandb -p sys_password

# For a full recovery, if restoring only to the time point of the backup set, it is necessary to delete archives
$ yasbak reset -c yashandb -p sys_password --with-arch
```

### yasbak clean

This command is used to clean up configuration files, metadata, and more generated during initialization.

|Option |Meaning |
| -------------- | ------------------------------- |
| *-c,--cluster*   | The cluster name specified during deployment       |
| *-f,--force*     | Ignore confirmation, directly clean               |
| *-p,--purge*     | Whether to delete cata log directory during cleanup   |

***Example***

```shell
$ yasbak clean -c yashandb --purge
```

### yasbak distribute

This command is used to distribute the backup set to the backup directory of the corresponding node.

|Option |Meaning |
| -------------- | ---------------------------- |
| *-c,--cluster*   | The cluster name specified during deployment    |
| *-b,--backup-set* | The path of the backup set compressed package            |

The format of the compressed package is as follows:

```shell
yashandb.tar.gz     # File name has no constraints, but the file extension must be .tar.gz
    mn-1-1.tar.gz   # The naming format for the compressed package is "node type-node id.tar.gz"
        full_01/... # If it is an incremental backup, multiple backup sets will be compressed into the same compressed package
    cn-2-1.tar.gz
        full_01/...
    dn-3-1.tar.gz
        full_01/...
```

***Example***

```shell
$ yasbak distribute -c yashandb -b yashandb.tar.gz
```

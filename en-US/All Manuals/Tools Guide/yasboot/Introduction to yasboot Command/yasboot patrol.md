> **Caution**: 
>
> You need to enable [OS authentication](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证) (which is enabled by default when following the standard installation steps) to use the patrol functionality properly.

## patrol strategy gen

This command generates a patrol strategy configuration file based on the provided options.

|Option |Meaning |
| ----------------------- | ------------------------ |
| *-c, --cluster*          | The cluster name of YashanDB (required)            |
| *-s, --strategy-name*    | Patrol strategy name (required)              |
| *-t, --strategy-type*    | Patrol strategy type:<br/>\* PERIOD: Periodic strategy, can be executed multiple times after application, default<br/>\* TIMING: Timed patrol strategy, executed once at scheduled time |
| *-ce,--cron-expression*   | Cron expression for setting patrol time, consisting of five fields representing minutes, hours, days, months, and weekdays |
| *-f,--frequency*         | Patrol frequency:<br/>\* monthly: Every month<br/>\* weekly: Every week, default<br/>\* daily: Every day<br/>\* hourly: Every hour |
| *-d,--days*              | Specific execution days for patrol    |
| *-st, --start-time*      | Specific start time for patrol    |
| *-sp,--store-path*       | Path to store patrol files      |
| *-sd,--store-days*       | Maximum retention days for patrol files, default keeps all   |
| *-sn,--store-num*        | Maximum retention number for patrol files, default keeps all   |
| *--yascheck-toml*        | yascheck.toml configuration file |
| *--sql-toml*             | sqlcollect.toml configuration file                |
| *--patrol-module*        | Patrol module, optional `host`, `sql`, and `gstack`, defaults to collecting all modules, separate multiple entries with `,` |
| *-F,--format*            | Format of the check results, options are json and html           |
| *--node-ids*             | Node ids for SQL collection, multiple entries can be provided, separated by `,`, defaults to `1-1` (can use `all` to collect all nodes) |
| *-o,--output*            | Path to save the generated configuration file, default is the current path |

```shell
# Set patrol time via `--cron-expression` and generate configuration file
$ yasboot patrol strategy gen -c yashandb -s ps01  --cron-expression "10 12 ? * 1,5,6,0" 

# Set patrol time via `--frequency`, `--days`, `--start-time` and generate configuration file
$ yasboot patrol strategy gen -c yashandb -s ps01  -f weekly -d 1,5-7  -st 12:10

# Generate configuration file for patrol modules host and gstack
$ yasboot patrol strategy gen -c yashandb -s ps01  --cron-expression "10 12 ? * 1,5,6,0" --patrol-module host,gstack
```

## patrol strategy add

This command adds and saves the patrol strategy to yasom.

|Option |Meaning |
| ------------ | ---------------------------- |
| *-t, --toml*   | Patrol strategy configuration file (required) |

***Example***

```shell
$ yasboot patrol strategy add -t ps01_patrolStrategy.toml 
```

## patrol strategy apply

This command applies the patrol strategy to database nodes.

|Option |Meaning |
| -------------------- | ---------------------------- |
| *-c, --cluster*       | The cluster name of YashanDB (required) |
| *-s,--strategy-name*  | Patrol strategy name (required)     |

***Example***

```shell
$ yasboot patrol strategy apply -c yashandb -s ps01 
```

## patrol strategy list

This command displays information about patrol strategies.

|Option |Meaning |
| -------------------- | ---------------------------- |
| *-c, --cluster*       | The cluster name of YashanDB (required) |
| *-s,--strategy-name*  | Patrol strategy name                 |
| *--size*              | Number of records per page, default 10     |
| *-p,--page*           | The page number to query               |

***Example***

```shell
# Display information of a specified patrol strategy
$ yasboot patrol strategy list -c yashandb -s ps01

# Display all patrol strategy information with pagination
$ yasboot patrol strategy list -c yashan
```

## patrol strategy cancel

This command cancels the application of a patrol strategy to the database.

|Option |Meaning |
| -------------------- | ---------------------------- |
| *-c, --cluster*       | The cluster name of YashanDB (required) |
| *-s,--strategy-name*  | Patrol strategy name (required)     |

***Example***

```shell
$ yasboot patrol strategy cancel -c yashandb -s ps01
```

## patrol strategy delete

This command deletes a patrol strategy.

|Option |Meaning |
| -------------------- | ---------------------------- |
| *-c, --cluster*       | The cluster name of YashanDB (required) |
| *-s,--strategy-name*  | Patrol strategy name (required)     |
| *-f,--force*          | Force delete                     |

***Example***

```shell
$ yasboot patrol strategy delete -c yashandb -s ps01
```

## patrol report list

This command displays patrol records with pagination.

|Option |Meaning |
| -------------------- | ---------------------------- |
| *-c, --cluster*       | The cluster name of YashanDB (required) |
| *-s,--strategy-name*  | Patrol strategy name                 |
| *--size*              | Number of records per page, default 10     |
| *-p,--page*           | The page number to query               |

***Example***

```shell
$ yasboot patrol report list -c yashan
```

## patrol report get

This command retrieves a patrol report.

|Option |Meaning |
| --------------- | ---------------------------- |
| *-c, --cluster*   | The cluster name of YashanDB (required) |
| *-u,--uuid*       | UUID of the patrol report (required)   |
| *-o,--output*     | Output path for the patrol report             |

***Example***

```shell
$ yasboot patrol report get -c yashandb -u 28d9d7d2c8ef7717
```

## patrol report delete

This command deletes a patrol collection.

|Option |Meaning |
| -------------------- | ---------------------------- |
| *-c, --cluster*       | The cluster name of YashanDB (required) |
| *-u,--uuid*           | Patrol report UUID                 |
| *-s,--strategy-name*  | Strategy name                     |
| *-w, --nowait*        | Do not wait for command execution results               |
| *-d, --child*         | Show task and subtask information |
| *--disable*           | Suppress progress bar display    |

Either the `-u` or `-s` parameter must be specified.

***Example***

```shell
# Delete patrol report with specified UUID
$ yasboot patrol report delete -c yashandb -u 28d9d7d2c8ef7717

# Delete all patrol reports generated by the specified patrol strategy
$ yasboot patrol report delete -c yashandb -s ps01
```

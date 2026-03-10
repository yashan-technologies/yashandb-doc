## job config gen

This command is used to generate job configuration files based on the input options.

|Option |Meaning |
|-------------------------|------------------------------------------------------|
| *-c, --cluster*           | The name of the YashanDB cluster (required)         |
| *--job-name*              | Job name (required)                                  |
| *-u, --user*  | Specify the database user. If not specified, the default user sys is used |
| *-p, --password*  | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication) (enabled by default after installation) is activated, no password needs to be specified   |
| *--sql*                   | SQL command (mutually exclusive with --sql-file)    |
| *--sql-file*              | SQL file (mutually exclusive with --sql)            |
| *-ce, --cron-expression*  | Cron expression for scheduling, consisting of five fields: minute, hour, day, month, week ("* * * * *") |
| *-f, --frequency*         | Job frequency<br/>\* monthly: monthly<br/>\* weekly: weekly, default<br/>\* daily: daily<br/>\* hourly: hourly |
| *-d, --days*              | Specific days for job execution, indicates which day of the month or week. Can specify multiple days, this parameter is effective only when --frequency is monthly or weekly<br/>\* when frequency is monthly, value range is [1,31]<br/>\* when frequency is weekly, value range is [1,7], 1 means Monday, 7 means Sunday, and so on |
| *-st, --start-time*       | Specific start time for the job<br/>\* when frequency is monthly, weekly, and daily, the format is `xx:xx`, representing hours and minutes, default is `00:00`<br/>\* when frequency is hourly, the format is `xx`, representing minutes, default is 0                |
| *--config-path*           | Path to save the generated configuration file, default is the current path  |

```shell
# Set the time using the --cron-expression parameter and generate the job configuration file
$ ./bin/yasboot job config gen -c yashandb --job-name test -u username -p password --sql "select * from v$instance;" --cron-expression "10 12 ? 1,5,6 *" 

# Set the time using the --frequency, --days, --start-time parameters and generate the job configuration file
$ ./bin/yasboot job config gen -c yashandb --job-name test -u username -p password --sql "select * from v$instance;"   -f weekly -d 1,5-7  -st 12:10
```

## job add

This command is used to add a job and save it to `YASOM` (currently does not support YAC/Distributed Cluster).

|Option |Meaning |
| ------------ |---------------|
| *-t, --toml*   | Job configuration file (required) |

***Example***

```shell
$ ./bin/yasboot job add -t job_yashandb_test.toml 
```

## job list

This command is used to display all job sets added through `YASOM`.

|Option |Meaning |
| --------------- |-----------|
| *-c, --cluster*   | The name of the YashanDB cluster (required)                 |
| *-n, --node-id*   | Node ID                            |
| *-d, --detail*    | Display detailed information       |
| *--size*          | The data quantity per page, default is 10 |
| *-p, --page*      | The page number to query           |
| *-S, --sort*      | The field by which to sort, default is create_at |
| *--order*         | The order of sorting, either asc or desc, default is desc       |
| *--search*        | Search by name field, format is `name=searchValue`<br/>Search fields support: job_id, job_name, and cron_expression |

***Example***

```shell
$ yasboot job list -c yashandb -d
```

## job apply

This command is used to apply a job to a database node (currently does not support YAC/Distributed Cluster).

|Option |Meaning |
|-----------------|--------------------|
| *-c, --cluster*     | The name of the YashanDB cluster (required) |
| *--job-name*        | Job name (required)                 |
| *-n, --node-id*     | Yasdb node ID (required)            |

***Example***

```shell
$ ./bin/yasboot job apply --job-name test -n 1-1 -c yashandb
```

## job update

This command is used to update an existing job.

|Option |Meaning |
| -------------------- | ---------------------------- |
| *-c, --cluster*       | The name of the YashanDB cluster (required) |
| *--job-name*          | Job name (required)                 |
| *-t, --toml*          | Job configuration file (required)   |

***Example***

```shell
$ ./bin/yasboot job update -t job_yashandb_test.toml --job-name test
```

## job cancel

This command is used to cancel the application of job strategies to the database.

|Option |Meaning |
| -------------------- | ---------------------------- |
| *-c, --cluster*       | The name of the YashanDB cluster (required) |
| *--job-name*          | Job name (required)                 |
| *-n, --node-id*       | Yasdb node ID (required)            |

***Example***

```shell
$ ./bin/yasboot job cancel --job-name test -c yashandb -n 1-1
```

## job delete

This command is used to delete job strategies (currently does not support YAC/Distributed Cluster).

|Option |Meaning |
| -------------------- | ---------------------------- |
| *-c, --cluster*       | The name of the YashanDB cluster (required) |
| *--job-name*          | Job name (required)                 |
| *-f, --force*         | Force delete                        |

***Example***

```shell
$ ./bin/yasboot job delete --job-name test -c yashandb -f
```

## job execute

This command is used to execute job strategies independently (not through scheduled tasks).

|Option |Meaning |
| -------------------- |--------------|
| *-c, --cluster*       | The name of the YashanDB cluster (required) |
| *--job-name*          | Job name (required)                 |
| *-n, --node-id*       | Yasdb node ID                       |

***Example***

```shell
$ ./bin/yasboot job execute --job-name test -c yashandb -n 1-1
```

## job show

Displays the time of job execution, whether it was successful, and the reason for failure.

|Option |Meaning |
| -------------------- |--------------------------------------------------|
| *-c, --cluster*       | The name of the YashanDB cluster (required) |
| *--job-name*          | Job name (required)                 |
| *-n, --node-id*       | Yasdb node ID (required)            |
| *-d, --detail*        | Display detailed information         |
| *--size*              | The data quantity per page, default is 10 |
| *-p, --page*          | The page number to query            |
| *--filter*            | Filter by the status column, with `success`, `running`, and `failed` three states |

***Example***

```shell
$ ./bin/yasboot job show -c yashandb -n 1-1 --job-name test
+----------------------------------------------------------------+
| id | job_id                           | job_name | node_id | status  | start_time          | completion_time     | hostid   | failed_reason                 |
+----------------------------------------------------------------+
| 2  | 65a8a0d8954d05c0fef16ef3021bf34a | test     | 1-1     | failed  | 2024-01-18 11:58:28 | 2024-01-18 11:58:28 | host0001 | node 1-1 exec job sql failed, stdout:[1:1]YAS-04231 keyword expected     |
+----+----------------------------------+----------+---------+---------+---------------------+---------------------+----------+------+
| 1  | 65a8a0d8954d05c0fef16ef3021bf34a | test     | 1-1     | success | 2024-01-18 11:55:00 | 2024-01-18 11:55:00 | host0001 | -       |
+----+----------------------------------+----------+---------+---------+---------------------+---------------------+----------+------+
```

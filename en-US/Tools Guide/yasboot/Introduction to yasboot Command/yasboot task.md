## task list

This command is used to obtain the task list of *yasboot*.

|Option |Meaning |
| --------------- | --------------------------------------------- |
| *-c, --cluster*  | The cluster name of YashanDB (required parameter) |
| *-s, --size*     | The number of records per page to query, default is 10 |
| *-p, --page*     | The page number to query                       |
| *-S, --sort*     | The field to sort by, default is create_at     |
| *-o, --order*    | The order to sort, options are asc or desc, default is desc |
| *--status*       | Filter by status                               |
| *--search*       | Search by name or type field                  |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
$ yasboot task list -c yashandb -p 1
+--------------------------------------------------------------------------------------------------------------------------------------------+
| uuid             | name               | type               | index   | hostid | status  | ret_code | progress | created_at          | cost |
+--------------------------------------------------------------------------------------------------------------------------------------------+
| ea08f07be2952adf | DeployYasdbCluster | DeployYasdbCluster | yashandb| -      | SUCCESS | 0        | 100      | 2023-03-09 14:49:51 | 131  |
+------------------+--------------------+--------------------+---------+--------+---------+----------+----------+---------------------+------+

# Query records where name field value is LogCollect
$ yasboot task list -c yashandb --search name=LogCollect
+----------------------------------------------------------------------------------------------------------------------------+
| uuid             | name       | type       | index   | hostid | status  | ret_code | progress | created_at          | cost |
+----------------------------------------------------------------------------------------------------------------------------+
| 4b3d9b8095e101a7 | LogCollect | LogCollect | yashandb| -      | SUCCESS | 0        | 100      | 2023-03-09 19:41:49 | 4    |
+------------------+------------+------------+---------+--------+---------+----------+----------+---------------------+------+
| c425b8dbbe0cd59b | LogCollect | LogCollect | yashandb| -      | SUCCESS | 0        | 100      | 2023-03-09 19:38:14 | 4    |
+------------------+------------+------------+---------+--------+---------+----------+----------+---------------------+------+

# Get tasks with status failed
$ yasboot task list -c yashandb --status failed

# Sort the task list by progress information, sorted in ascending order of completion
$ yasboot task list -c yashandb -S progress -o asc
```

## task get

This command is used to obtain the basic information of a single task.

|Option |Meaning |
| ------------- | ---------------- |
| -c, --cluster   | The cluster name of YashanDB (required parameter) |
| -u, --uuid      | The UUID of the task (required parameter) |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
$ ./bin/yasboot task get -c yashandb -u ea08f07be2952adf
{
  "uuid": "ea08f07be2952adf",
  "parentUuid": "",
  "name": "DeployYasdbCluster",
  "index": "yashandb",
  "hostid": "",
  "manageIp": "",
  "hostName": "",
  "result": {
    "RetCode": 0,
    "Stdout": "",
    "Stderr": ""
  },
  "mask": "yashandb.DeployYasdbCluster:",
  "status": 3,
  "progress": 100,
  "args": null,
  "depends": null,
  "startTime": 1678344591518,
  "endTime": 1678344722069,
  "runType": 0,
  "invisible": 0,
  "taskType": 0,
  "supportRollback": false,
  "step": -1,
  "allowFailed": false,
  "id": 1,
  "createdAt": "2023-03-09 14:49:51",
  "updatedAt": "",
  "Children": [
    "ff1517939d16a436",
    "723d177c231a3369",
    "88d6551508c82abe",
    "643600fdf550d01f",
    "9cfe337bba36a44c",
    "cbb13176b9a2f6fe",
    "e978128f1ad61d3f",
    "c5da24f954d79839"
  ]
}
```

## task watch

This command is used to view the task running status.

|Option |Meaning |
| --------------- | ---------------------- |
| *-c, --cluster*  | The cluster name of YashanDB (required parameter) |
| *--disable*      | Suppress the display of task running progress |
| *-d, --child*    | Display task and child task information |
| *-u, --uuid*     | The unique identifier of the task (required parameter) |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
$ yasboot task watch -c yashandb -u ff1517939d16a436
```

## task cancel

This command is used to cancel a running task.

|Option |Meaning |
| --------------- | ----------------------------------------- |
| *-c, --cluster*  | The cluster name of YashanDB (required parameter) |
| *--disable*      | Suppress the display of task running progress |
| *-u, --uuid*     | The unique identifier of the task (required parameter) |
| *-w, --nowait*   | Do not wait for the command running result to be displayed, default is false |
| *-d, --child*    | Display the basic information of the task and its child tasks, default is false |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
$ yasboot task cancel -c yashandb -u ff1517939d16a436 -w -d
```

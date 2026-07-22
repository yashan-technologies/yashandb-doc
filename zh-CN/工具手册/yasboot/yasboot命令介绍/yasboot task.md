## task list

本命令用于获取yasboot的任务列表。

|  选项| 含义|
| --------------- | --------------------------------------------- |
| *-c, --cluster* | YashanDB的集群名（必传参数）                              |
| *\-s, --size*   | 查询的每页数据量，默认10                      |
| \-p, --page     | 需要查询的页码                                |
| *-S, --sort*    | 以哪一个字段来排序，默认是create_at           |
| *-o, --order*   | 以哪种方式来排序，可选为asc或desc，默认是desc |
| *--status*      | 以状态进行过滤                                |
| *--search*      | 按name或者type字段搜索                                |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$ yasboot task list -c yashandb -p 1
+--------------------------------------------------------------------------------------------------------------------------------------------+
| uuid             | name               | type               | index   | hostid | status  | ret_code | progress | created_at          | cost |
+--------------------------------------------------------------------------------------------------------------------------------------------+
| ea08f07be2952adf | DeployYasdbCluster | DeployYasdbCluster | yashandb| -      | SUCCESS | 0        | 100      | 2023-03-09 14:49:51 | 131  |
+------------------+--------------------+--------------------+---------+--------+---------+----------+----------+---------------------+------+

# 查询name字段值为LogCollect的记录
$ yasboot task list -c yashandb --search name=LogCollect
+----------------------------------------------------------------------------------------------------------------------------+
| uuid             | name       | type       | index   | hostid | status  | ret_code | progress | created_at          | cost |
+----------------------------------------------------------------------------------------------------------------------------+
| 4b3d9b8095e101a7 | LogCollect | LogCollect | yashandb| -      | SUCCESS | 0        | 100      | 2023-03-09 19:41:49 | 4    |
+------------------+------------+------------+---------+--------+---------+----------+----------+---------------------+------+
| c425b8dbbe0cd59b | LogCollect | LogCollect | yashandb| -      | SUCCESS | 0        | 100      | 2023-03-09 19:38:14 | 4    |
+------------------+------------+------------+---------+--------+---------+----------+----------+---------------------+------+

# 获取状态为失败的任务
$ yasboot task list -c yashandb --status failed

# 对任务列表以进度信息进行排序，进度完成度从小到大排序
$ yasboot task list -c yashandb -S progress -o asc
```

## task get

本命令用于获取单个任务的基本信息。

|  选项| 含义|
| ------------- | ---------------- |
| -c, --cluster | YashanDB的集群名（必传参数） |
| -u, --uuid    | 任务的uuid（必传参数）       |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

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

本命令用于查看任务运行状态。

|  选项| 含义|
| --------------- | ---------------------- |
| *-c, --cluster* | YashanDB的集群名（必传参数）       |
| *--disable*     | 屏蔽任务运行的进度展示 |
| *-d, --child*   | 展示任务以及子任务信息 |
| *-u, --uuid*    | 任务的唯一标识uuid（必传参数）     |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$ yasboot task watch -c yashandb -u ff1517939d16a436
```

## task cancel

本命令用于取消正在运行中的任务。

|  选项| 含义|
| --------------- | ----------------------------------------- |
| *-c, --cluster* | YashanDB的集群名（必传参数）                          |
| *--disable*     | 屏蔽任务运行的进度展示                    |
| *-u, --uuid*    | 任务的唯一标识uuid（必传参数）                        |
| *-w, --nowait*  | 不等待命令运行结果展示，默认为false       |
| *-d, --child*   | 展示任务以及子任务的基本信息，默认为false |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$ yasboot task cancel -c yashandb -u ff1517939d16a436 -w -d
```

## job config gen

本命令用于根据输入的各选项生成job配置文件。

| 选项                      | 含义                                                                                                                                 |
|-------------------------|------------------------------------------------------------------------------------------------------------------------------------|
| *-c, --cluster*         | YashanDB的集群名（必传参数）                                                                                                                 |
| *--job-name*            | job名称 （必传参数）                                                                                                                       |
| *-u,--user*             | 集群的用户名                                                                                                                        |
| *-p,--password*         | 集群的用户名对应的密码                                                                                                                           |
| *--sql*                 | SQL命令（与--sql-file互斥）                                                                                                               |
| *--sql-file*            | SQL文件（与--sql互斥）                                                                                                                    |
| *-ce,--cron-expression* | cron表达式，用于设置时间，由五个字段组成，分别表示分、小时、日期、月份、星期（“* * * * *” ）                                                                             |
| *-f,--frequency*        | job频率<br/>\* monthly：每月<br/>\* weekly：每周，默认值<br/>\* daily：每天<br/>\* hourly：每小时                                                    |
| *-d,--days*             | job具体的执行天数，表示每月或每周的第几天，可以填写多天，仅频率--frequency为monthly、weekly时，该参数有效<br/>\* 当频率为monthly，取值范围为[1,31]<br/>\* 当频率为weekly，取值范围为[1,7]，1表示星期一，7表示星期日，以此类推 |
| *-st, --start-time*     | job的具体开始时间<br/>\*当频率为monthly、weekly和daily时，格式为`xx:xx`，表示小时和分钟，默认为`00:00`<br/>\*当频率为hourly，格式为`xx`，表示分钟，默认为0                      |                                                         ||
| *--config-path*         | 生成的配置文件保存路径，默认为当前路径                                                                                                                |

```shell
# 通过参数--cron-expression设置时间并生成job配置文件
$ ./bin/yasboot job config gen -c yashandb --job-name test -u username -p password --sql "select * from v$instance;" --cron-expression "10 12 ? 1,5,6 *" 

# 通过参数--frquency、--days、--start-time设置时间并生成job配置文件
$ ./bin/yasboot job config gen -c yashandb --job-name test -u username -p password --sql "select * from v$instance;"   -f weekly -d 1,5-7  -st 12:10

```

## job add

本命令用于将job添加并保存到`YASOM`中（暂不支持共享集群）。

| 选项         | 含义            |
| ------------ |---------------|
| *-t, --toml* | job配置文件（必传参数） |

示例

```shell
$ ./bin/yasboot job add -t job_yashandb_test.toml 
```

## job list

本命令用于展示所有通过`YASOM`添加的job集。

| 选项            | 含义                                                                            |
| --------------- |-------------------------------------------------------------------------------|
| *-c, --cluster* | YashanDB的集群名（必传参数）                                                            |
| *-n,--node-id*  | 节点ID                                                                         |
| *-d,--detail*   | 展示详细信息                                                                        |
| *--size*        | 查询的每页数据量，默认为10                                                                 |
| *-p,--page*     | 需要查询的页码                                                                       |
| *-S,--sort*     | 以哪一个字段来排序，默认为create_at                                                        |
| *--order*       | 以哪种方式来排序，可选为asc或desc，默认为desc                                                  |
| *--search*      | 按name字段搜索，格式为`name=searchValue`<br/>支持搜索字段有：job_id、job_name和cron_expression |

示例

```shell
$ yasboot job list -c yashandb -d
```

## job apply

本命令用于将job应用到数据库节点（暂不支持共享集群）。

| 选项              | 含义                 |
|-----------------|--------------------|
| *-c, --cluster* | YashanDB的集群名（必传参数） |
| *--job-name* | job名称（必传参数）        |
| *-n,--node-id*  | yasdb的nodeid（必传参数） |

示例

```shell
$ ./bin/yasboot job apply --job-name test -n 1-1 -c yashandb
```

## job update

本命令用于更新已添加的job。

| 选项                 | 含义                         |
| -------------------- | ---------------------------- |
| *-c, --cluster*      | YashanDB的集群名（必传参数） |
| *--job-name* | job名称（必传参数）        |
| *-t, --toml* | job配置文件（必传参数） |


示例

```shell
$ ./bin/yasboot job update -t job_yashandb_test.toml --job-name test
```

## job cancel

本命令用于取消应用job策略到数据库。

| 选项                 | 含义                         |
| -------------------- | ---------------------------- |
| *-c, --cluster*      | YashanDB的集群名（必传参数） |
| *--job-name* | job名称（必传参数）        |
| *-n,--node-id*  | yasdb的nodeid（必传参数） |

示例

```shell
$ ./bin/yasboot job cancel --job-name test -c yashandb -n 1-1
```

## job delete

本命令用于删除job策略（暂不支持共享集群）。

| 选项                 | 含义                         |
| -------------------- | ---------------------------- |
| *-c, --cluster*      | YashanDB的集群名（必传参数） |
| *--job-name* | job名称（必传参数）        |
| *-f,--force*         | 强制删除            |

示例

```shell
$ ./bin/yasboot job delete --job-name test -c yashandb -f
```

## job execute

本命令用于单独执行job策略（不走定时任务）。

| 选项                 | 含义           |
| -------------------- |--------------|
| *-c, --cluster*      | YashanDB的集群名（必传参数） |
| *--job-name* | job名称（必传参数）  |
| *-n,--node-id*  | yasdb的nodeid |

示例

```shell
$ ./bin/yasboot job execute --job-name test -c yashandb -n 1-1
```

## job show

展示job执行的时间、是否成功以及失败的原因。

| 选项                 | 含义                                               |
| -------------------- |--------------------------------------------------|
| *-c, --cluster*      | YashanDB的集群名（必传参数）                               |
| *--job-name* | job名称（必传参数）                                      |
| *-n,--node-id*  | yasdb的nodeid（必传参数）                               |
| *-d,--detail*   | 展示详细信息                                           |
| *--size*        | 查询的每页数据量，默认为10                                    |
| *-p,--page*     | 需要查询的页码                                          |
| *--filter*     | 根据status列的结果过滤，有`success`、`running`和`failed`3种状态 |

示例

```shell
$ ./bin/yasboot job show -c yashandb -n 1-1 --job-name test
+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| id | job_id                           | job_name | node_id | status  | start_time          | completion_time     | hostid   | failed_reason                                                            |
+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| 2  | 65a8a0d8954d05c0fef16ef3021bf34a | test     | 1-1     | failed  | 2024-01-18 11:58:28 | 2024-01-18 11:58:28 | host0001 | node 1-1 exec job sql failed, stdout:[1:1]YAS-04231 keyword expected     |
+----+----------------------------------+----------+---------+---------+---------------------+---------------------+----------+--------------------------------------------------------------------------+
| 1  | 65a8a0d8954d05c0fef16ef3021bf34a | test     | 1-1     | success | 2024-01-18 11:55:00 | 2024-01-18 11:55:00 | host0001 | -                                                                        |
+----+----------------------------------+----------+---------+---------+---------------------+---------------------+----------+--------------------------------------------------------------------------+
```

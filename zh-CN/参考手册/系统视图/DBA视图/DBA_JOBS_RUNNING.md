本视图显示当前正在运行的JOB信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| SID | INTEGER | 执行JOB的会话ID |
| JOB | BIGINT | JOB的ID，由系统自动分配，与DBA_OBJECTS中的OBJECT_ID相同 |
| FAILURES | INTEGER | 从JOB创建至查询视图期间，JOB执行失败的总次数 |
| LAST_DATE | DATE | JOB上次成功执行的日期 |
| LAST_SEC | VARCHAR(8) | JOB上次成功执行的时间 |
| THIS_DATE | DATE | JOB当前开始执行的日期 |
| THIS_SEC | VARCHAR(8) | JOB当前开始执行的时间 |
| INSTANCE | INTEGER | 集群中可以执行JOB的实例ID。默认值是0，表示可以在任意实例执行 |

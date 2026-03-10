本视图显示所有DBMS_JOB高级包创建的JOB对象信息。

| 字段        | 类型                       | 说明                                                    |
| :---------- |:-------------------------| :------------------------------------------------------ |
| JOB         | BIGINT                   | JOB的ID，由系统自动分配，与DBA_OBJECTS中的OBJECT_ID相同 |
| LOG_USER    | VARCHAR(64)              | 创建JOB时登录的用户                                     |
| PRIV_USER   | VARCHAR(64)              | 赋予JOB权限的用户                                       |
| SCHEMA_USER | VARCHAR(64)              | JOB所有者的用户名                                       |
| LAST_DATE   | TIMESTAMP WITH TIME ZONE | JOB上一次执行的时间                                     |
| LAST_SEC    | VARCHAR(8)               | 同LAST_DATE                                             |
| THIS_DATE   | TIMESTAMP WITH TIME ZONE | 如果JOB正在执行，则为本次开始执行的时间。通常为NULL     |
| THIS_SEC    | VARCHAR(8)               | 同THIS_DATE                                             |
| NEXT_DATE   | TIMESTAMP WITH TIME ZONE | JOB下次执行的时间                                       |
| NEXT_SEC    | VARCHAR(8)               | 同NEXT_DATE                                             |
| TOTAL_TIME  | INTEGER                  | 最后一次执行持续的时间（单位：秒）                        |
| MVIEW_CREATE| VARCHAR(5)               | JOB是否为物化视图创建时内部生成（TRUE/FALSE）            |
| BROKEN      | CHAR(1)                  | 表示JOB是否被破坏（Y/N）                                |
| INTERVAL    | VARCHAR(4000)            | 用于计算NEXT_DATE的表达式                               |
| FAILURES    | INTEGER                  | 从JOB创建至查询视图期间，JOB执行失败的总次数                          |
| WHAT        | VARCHAR(4000)            | JOB执行的动作                                           |
| INSTANCE    | INTEGER                  | 集群中可以执行JOB的实例ID。默认值是0，表示可以在任意实例执行  |
| RUNNING_INSTANCE | INTEGER                  | 显示后台执行JOB的实例ID，非RUNNING状态显示0               |
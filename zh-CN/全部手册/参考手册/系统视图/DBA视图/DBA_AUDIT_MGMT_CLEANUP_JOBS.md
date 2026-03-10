本视图显示审计日志清理的定时任务信息。

|  字段| 类型| 说明|
| ------------------- |------------------------------|----------------------------------------------------------------------------------------|
| OWNER               | VARCHAR(64)                  | JOB的所有者                                                                                |
| JOB_NAME            | VARCHAR(64)                  | JOB名称                                                                                  |
| JOB_SUBNAME         | VARCHAR(64)                  | 保留字段                                                                                   |
| JOB_STYLE           | CHAR(7)                      | JOB类型，目前只支持REGULAR                                                                     |
| JOB_CREATOR         | VARCHAR(64)                  | 创建JOB的用户名                                                                              |
| CLIENT_ID           | VARCHAR(64)                  | 保留字段                                                                                   |
| PROGRAM_OWNER       | VARCHAR(4000)                | 保留字段                                                                                   |
| PROGRAM_NAME        | VARCHAR(4000)                | 保留字段                                                                                   |
| JOB_TYPE            | VARCHAR(16)                  | JOB Action的类型：<br>\* PLSQL_BLOCK：匿名块<br>\* STORED_PROCEDURE：存储过程                       |
| JOB_ACTION          | VARCHAR(4000)                | JOB执行的动作                                                                               |
| NUMBER_OF_ARGUMENTS | INTEGER                      | JOB的参数个数                                                                               |
| SCHEDULE_OWNER      | VARCHAR(4000)                | 保留字段                                                                                   |
| SCHEDULE_NAME       | VARCHAR(4000)                | 保留字段                                                                                   |
| SCHEDULE_TYPE       | CHAR(5)                      | 保留字段                                                                                   |
| START_DATE          | TIMESTAMP(6) WITH TIME ZONE     | JOB开始执行的时间                                                                             |
| REPEAT_INTERVAL     | VARCHAR(4000)                | JOB执行的时间间隔                                                                             |
| END_DATE            | TIMESTAMP(6) WITH TIME ZONE     | JOB结束执行的时间                                                                             |
| ENABLED             | BOOLEAN                      | JOB是否生效                                                                                |
| AUTO_DROP           | VARCHAR(5)                   | JOB完成后是否自动删除（TRUE/FALSE）                                                               |
| STATE               | VARCHAR(9)                   | JOB当前的状态：<br>\* DISABLED：失效<br>\* SCHEDULED：已计划 <br>\* RUNNING：运行中<br>\* COMPLETED：已完成 |
| RUN_COUNT           | INTEGER                      | JOB已经执行的次数                                                                             |
| MAX_RUNS            | INTEGER                      | JOB最大执行的次数                                                                             |
| FAILURE_COUNT       | INTEGER                      | JOB失败的次数                                                                               |
| MAX_FAILURES        | INTEGER                      | JOB允许失败的次数，失败次数超过该值后，JOB将被标记为失效                                                        |
| RETRY_COUNT         | INTEGER                      | JOB失败后重试的次数                                                                            |
| LAST_START_DATE     | TIMESTAMP(6) WITH TIME ZONE     | JOB最后一次开始执行的时间                                                                         |
| LAST_RUN_DURATION   | INTERVAL DAY(9) TO SECOND(6) | JOB最后一次执行持续的时间（单位：秒）                                                                   |
| NEXT_RUN_DATE       | TIMESTAMP(6) WITH TIME ZONE     | JOB下次执行的时间                                                                             |
| MAX_RUN_DURATION    | INTERVAL DAY(3) TO SECOND(0) | 保留字段                                                                                   |
| COMMENTS            | VARCHAR(4000)                | 用户为JOB添加的描述信息                                                                          |

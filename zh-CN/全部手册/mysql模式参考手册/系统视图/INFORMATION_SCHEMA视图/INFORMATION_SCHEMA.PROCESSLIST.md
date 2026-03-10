本视图显示数据库服务中正在执行的会话线程及正在执行的操作。


|  字段| 类型| 说明|
| --- | --- | --- |
| ID      | bigint unsigned |会话ID    |
| USER    | varchar(64)     |建立该会话的用户   |
| HOST    | varchar(57)     |建立该会话的客户端IP及端口   |
| DB      | varchar(64)     |该会话连接的数据库实例   |
| COMMAND | varchar(16)     |该会话正在执行的命令的类型或会话状态。<br>Query：该会话正在执行查询SQL语句<br>Daemon：后台常驻<br>Sleep：该会话已休眠<br>Connect：该会话正在建立连接   |
| TIME    | int(38)         |该会话处于当前状态的累计时间，单位为秒   |
| STATE   | varchar(28)     |显示当前正在执行的SQL语句的执行状态   |
| INFO    | varchar(1000)   |显示当前正在执行的SQL语句   |

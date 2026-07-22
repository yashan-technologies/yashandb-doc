```ebnf
px_obj = PX_OBJ "(" sessionid ")" .
```

PX_OBJ表函数根据输入的sessionid，查询返回该连接会话下的并行stage信息。

其中sessionid的值必须为INT类型。

**sessionid**

在分布式场景下为全局sessionid，可以通过gv$session视图查得。

在单机场景下为当前节点的session编号，可以通过v$session视图查得。

返回值：函数执行返回组成并行stage信息的表。px_obj表函数返回信息如下：

|  字段| 类型| 说明|
| --- | --- | --- |
| SID | INTEGER | 分布式下为全局session id |
| SERIAL# | INTEGER |  |
| SEQ\_NO | INTEGER | sequence number |
| SQL\_ID | VARCHAR | 对应SQL |
| THREAD\_ID | BIGINT | 对应执行线程ID |
| STAGE\_ID | SMALLINT | 对应stage id |
| BROTHER\_ID | SMALLINT | 位于同级stage的位置id |
| TYPE | VARCHAR | sender或者receiver |
| SENDER\_TYPE | INTEGER | 对应计划中的sender type RANDOM，HASH，BROADCAST等 |
| TQ\_ID | SMALLINT | table queue id |
| RPORT | SMALLINT | 正在接收端口号 receiver有效 |
| WPORT | SMALLINT | 正在发送端口号 sender有效 |
| LOCAL\_ENDPOINT | SMALLINT | stage所处节点enpoint |
| REMOTE\_ENDPOINT | SMALLINT | 远端节点endpoint |
| REMOTE\_GROUP | SMALLINT | 远端节点组ID |
| STATUS | INTEGER | PX状态 INIT：初始状态  BUSY：完成握手  FINISH：正常发送完毕  ABORT：被终止 |
| WORKING\_REMOTE\_PORTS | SMALLINT | 活跃中的远程通道数量 |
| MIN\_WINDOW\_BLOCKS | INTEGER | 最小滑动窗口剩余block数 |
| MIN\_WINDOW\_PORT | SMALLINT | 最小滑动窗口对应端口号 |
| MAX\_WINDOW\_BLOCKS | INTEGER | 最大滑动窗口剩余block数 |
| MAX\_WINDOW\_PORT | SMALLINT | 最大滑动窗口对应端口号 |
| PENDING\_BLOCKS | INTEGER | 待处理block数 |
| HOLD\_MEMORY | INTEGER | 占用内存大小 |
| SEND\_NUM | BIGINT | 发送次数 sender有效 |
| SEND\_SIZE | BIGINT | 发送数据大小 sender有效 |
| RECV\_NUM | BIGINT | 接收次数 receiver有效 |
| RECV\_SIZE| BIGINT | 接收数据大小 receiver有效 |
| RECV\_ROWS | BIGINT | 接收记录数 receiver有效 |
| TIMEOUT\_TIMES | INTEGER | 超时次数 receiver时为接收数据的等待超时次数 sender时为等待滑动窗口配额超时次数 |
| LAST\_ACTIVE\_TIME | TIMESTAMP | 最后活动时刻 receiver时为最后收到数据的时间 sender时为最后发出数据的时间|

示例（存算一体分布式集群部署）

```sql
--根据实际场景获取一个全局会话ID
SELECT global_session_id FROM GV$SESSION;

--查询该连接会话下的tabqueue通道信息
SQL> SELECT SID, SQL_ID, THREAD_ID, STAGE_ID, TYPE FROM TABLE(px_obj(131094));

         SID SQL_ID                        THREAD_ID STAGE_ID TYPE             
------------ ----------------- --------------------- -------- -----------------
      131094 7x36y59y3gkwu                      7404       -1 receiver         
      131094 7x36y59y3gkwu                      7404       -1 receiver         
      131094 7x36y59y3gkwu                      7391        0 sender           
      131094 7x36y59y3gkwu                      7391        0 sender           
      131094 7x36y59y3gkwu                      7391        0 sender           
      131094 7x36y59y3gkwu                      8851        1 sender           
      131094 7x36y59y3gkwu                      8851        1 sender           
      131094 7x36y59y3gkwu                      8851        1 sender           
      131094 7x36y59y3gkwu                      7388        0 sender           
      131094 7x36y59y3gkwu                      7393        0 sender           
      131094 7x36y59y3gkwu                      7393        0 sender           
      131094 7x36y59y3gkwu                      7393        1 sender           
      131094 7x36y59y3gkwu                      8852        1 sender           
      131094 7x36y59y3gkwu                      8852        1 sender           

14 rows fetched.
```

```ebnf+diagram
px_channel::= PX_CHANNEL "(" sessionid ")" 
```

PX_CHANNEL表函数根据输入的sessionid，查询返回该连接会话下的tabqueue通道信息。

其中sessionid的值必须为INT类型。

**sessionid**

在分布式场景下为全局sessionid，可以通过GV$SESSION视图查得。

在单机场景下为当前节点的session编号，可以通过V$SESSION视图查得。

返回值：函数执行返回组成tabqueue通道信息的表。px_channel表函数返回信息如下：

|  字段| 类型| 说明|
| --- | --- | --- |
| SID | INTEGER | 分布式下为全局session id |
| SERIAL# | INTEGER |  |
| SEQ_NO | INTEGER | sequence number |
| SQL_ID | VARCHAR | 对应SQL |
| STAGE_ID | SMALLINT | 对应stage id |
| TQ_ID | SMALLINT | table queue id |
| TYPE | VARCHAR | reader或writer |
| IS_LOCAL | VARCHAR | 是否进程内通道，两端endpoint都是本地endpoint。 |
| WPORT | SMALLINT | 写端口号 |
| RPORT | SMALLINT | 读端口号 |
| WPORT_ENDPOINT | SMALLINT | 写端对应节点endpoint |
| RPORT_ENDPOINT | SMALLINT | 读端对应节点endpoint |
| LINK_ID | SMALLINT | 对应ICS链路ID。reader时对应接收链路ID；writer时对应发送链路ID。 |
| WINDOW_SIZE | INTEGER | 滑动窗口总大小 |
| WINDOW_ACK_ID | INTEGER | 滑窗可用block id |
| WINDOW_CURR_ID | INTEGER | 滑窗当前使用block id |
| PENDING_BLOCKS | INTEGER | 待处理block数 |
| HOLD_MEMORY | INTEGER | 占用内存大小 |
| WRITE_NUM | BIGINT | 写次数 writer有效 |
| WRITE_SIZE | BIGINT | 写数据大小 writer有效 |
| READ_NUM | BIGINT | 读次数 reader有效 |
| READ_SIZE| BIGINT | 读数据大小 reader有效 |
| READ_ROWS | BIGINT | 读记录数 reader有效 |
| TIMEOUT_TIMES | INTEGER | 超时次数 reader时为接收数据的等待超时次数 writer时为等待滑动窗口配额超时次数 |
| LAST_ACTIVE_TIME | TIMESTAMP | 最后活动时刻 reader时为最后收到数据的时间 writer时为最后发出数据的时间|
| MAX_BLOCK_WAIT_TIME | BIGINT | 获取block最长等待时间 |
| AVG_BLOCK_WAIT_TIME | BIGINT | 获取block平均等待时间 |
| MAX_CHANNEL_WAIT_TIME | BIGINT | 数据入列到出列最长耗时 |
| AVG_CHANNEL_WAIT_TIME | BIGINT | 数据入列到出列平均耗时 |

示例（存算一体分布式集群部署）

```sql
--根据实际场景获取一个全局会话ID
SELECT global_session_id FROM GV$SESSION;

--查询该连接会话下的tabqueue通道信息
SELECT SID, SQL_ID, STAGE_ID, TQ_ID, TYPE, IS_LOCAL FROM TABLE(PX_CHANNEL(131091));

         SID SQL_ID                            STAGE_ID    TQ_ID TYPE      IS_LOCAL                     
------------ --------------------------------- -------- -------- --------- ---------                    
      131091 6w7vgtqwc018p                           -1        0 READER    N                            
      131091 6w7vgtqwc018p                           -1        0 READER    N                            
      131091 6w7vgtqwc018p                           -1        0 READER    N                            
      131091 6w7vgtqwc018p                           -1        0 READER    N                            
      131091 6w7vgtqwc018p                           -1        0 READER    N                            
      131091 6w7vgtqwc018p                            0        0 WRITER    N                            
      131091 6w7vgtqwc018p                            0        0 WRITER    N                            
      131091 6w7vgtqwc018p                            0        0 WRITER    N                            
      131091 6w7vgtqwc018p                            0        0 WRITER    N                            
      131091 6w7vgtqwc018p                            0        0 WRITER    N                            
      131091 6w7vgtqwc018p                            0        0 WRITER    N                            

```

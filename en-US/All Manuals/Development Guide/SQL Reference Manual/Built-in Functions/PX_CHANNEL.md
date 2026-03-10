```ebnf+diagram
px_channel::= PX_CHANNEL "(" sessionid ")" 
```

The PX_CHANNEL table function queries and returns the tabqueue channel information for the given connection session based on the input sessionid.

The value of sessionid must be of INT type.

**sessionid**

In a distributed scenario, it is a global session id, which can be obtained from the GV$SESSION view.

In a standalone deployment scenario, it is the session number of the current node, which can be found through the V$SESSION view.

Return value: The function returns a table that composes the tabqueue channel information. The px_channel table function returns information as follows:

|Field |Type |Description |
| --- | --- | --- |
| SID     | INTEGER  | Global session id in a distributed context         |
| SERIAL# | INTEGER |  |
| SEQ_NO | INTEGER | sequence number |
| SQL_ID  | VARCHAR  | Corresponding SQL                                  |
| STAGE_ID| SMALLINT | Corresponding stage id                             |
| TQ_ID | SMALLINT | table queue id |
| TYPE    | VARCHAR  | Reader or Writer                                   |
| IS_LOCAL| VARCHAR  | Indicates whether the channel is in-process, where both endpoints are local endpoints. |
| WPORT   | SMALLINT | Write port number                                  |
| RPORT   | SMALLINT | Read port number                                   |
| WPORT_ENDPOINT | SMALLINT | Endpoint for the corresponding write node      |
| RPORT_ENDPOINT | SMALLINT | Endpoint for the corresponding read node       |
| LINK_ID | SMALLINT | Corresponding ICS link ID. Corresponds to receive link ID for reader; send link ID for writer. |
| WINDOW_SIZE | INTEGER | Total size of sliding window                      |
| WINDOW_ACK_ID | INTEGER | Available block ID of sliding window             |
| WINDOW_CURR_ID | INTEGER | Current block ID in use of sliding window      |
| PENDING_BLOCKS | INTEGER | Number of blocks to be processed                |
| HOLD_MEMORY | INTEGER | Memory usage size                                 |
| WRITE_NUM | BIGINT | Write count, effective for writer                 |
| WRITE_SIZE | BIGINT | Size of written data, effective for writer       |
| READ_NUM | BIGINT | Read count, effective for reader                  |
| READ_SIZE| BIGINT | Size of read data, effective for reader          |
| READ_ROWS| BIGINT | Number of rows read, effective for reader        |
| TIMEOUT_TIMES | INTEGER | Number of timeouts; for reader it refers to the wait timeout for receiving data; for writer it refers to the timeout for waiting the sliding window quota. |
| LAST_ACTIVE_TIME | TIMESTAMP | Last active time; for reader, it is the last time data was received; for writer, it is the last time data was sent. |
| MAX_BLOCK_WAIT_TIME | BIGINT | Longest wait time to obtain block                |
| AVG_BLOCK_WAIT_TIME | BIGINT | Average wait time to obtain block               |
| MAX_CHANNEL_WAIT_TIME | BIGINT | Longest time from data entering the queue to exiting. |
| AVG_CHANNEL_WAIT_TIME | BIGINT | Average time from data entering the queue to exiting. |

***Example*** for ISC Distributed Cluster Deployment

```sql
-- Obtain a global session ID based on the actual scenario
SELECT global_session_id FROM GV$SESSION;

-- Query the tabqueue channel information under this connection session
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

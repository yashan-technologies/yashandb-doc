```ebnf+diagram
px_obj::= PX_OBJ "(" sessionid ")" 
```

The PX_OBJ function queries and returns parallel stage information for the connection session based on the input sessionid.

The value of sessionid must be of INT type.

**sessionid**

In a distributed scenario, it is a global sessionid, which can be obtained through the GV$SESSION view.

In a standalone scenario, it is the session number of the current node, which can be obtained through the V$SESSION view.

Return value: The function execution returns a table consisting of parallel stage information. The px_obj function returns the following information:

|Field |Type |Description |
| --- | --- | --- |
| SID          | INTEGER   | Global session ID in distributed case         |
| SERIAL# | INTEGER |  |
| SEQ_NO | INTEGER | sequence number |
| SQL_ID       | VARCHAR   | Corresponding SQL                             |
| THREAD_ID    | BIGINT    | Corresponding execution thread ID             |
| STAGE_ID     | SMALLINT  | Corresponding stage ID                        |
| BROTHER_ID   | SMALLINT  | Position ID of the same-level stage          |
| TYPE         | VARCHAR   | sender or receiver                            |
| SENDER_TYPE  | INTEGER   | Corresponding sender type in the plan: RANDOM, HASH, BROADCAST, etc. |
| TQ_ID | SMALLINT | table queue id |
| RPORT        | SMALLINT  | Receiving port number, valid for receiver    |
| WPORT        | SMALLINT  | Sending port number, valid for sender        |
| LOCAL_ENDPOINT| SMALLINT | stage's node endpoint                         |
| REMOTE_ENDPOINT| SMALLINT| Remote node endpoint                          |
| REMOTE_GROUP | SMALLINT  | Remote node group ID                         |
| STATUS       | INTEGER   | PX status INIT: initial state, BUSY: handshake complete, FINISH: normal sending completed, ABORT: terminated |
| WORKING_REMOTE_PORTS | SMALLINT | Number of active remote channels       |
| MIN_WINDOW_BLOCKS | INTEGER | Minimum remaining block count in sliding window |
| MIN_WINDOW_PORT | SMALLINT | Port number corresponding to the minimum sliding window |
| MAX_WINDOW_BLOCKS | INTEGER | Maximum remaining block count in sliding window |
| MAX_WINDOW_PORT | SMALLINT | Port number corresponding to the maximum sliding window |
| PENDING_BLOCKS | INTEGER  | Number of pending blocks                      |
| HOLD_MEMORY    | INTEGER   | Occupied memory size                          |
| SEND_NUM       | BIGINT    | Sending count, valid for sender              |
| SEND_SIZE      | BIGINT    | Sending data size, valid for sender          |
| RECV_NUM       | BIGINT    | Receiving count, valid for receiver           |
| RECV_SIZE      | BIGINT    | Receiving data size, valid for receiver       |
| RECV_ROWS      | BIGINT    | Number of received records, valid for receiver|
| TIMEOUT_TIMES  | INTEGER   | Timeout count. For receiver, it is the timeout count for waiting to receive data, for sender, it is the timeout count for waiting sliding window quota |
| LAST_ACTIVE_TIME | TIMESTAMP | Last active moment; for receiver, it is the time of the last received data, for sender, it is the time of the last sent data |

***Example*** for ISC Distributed Cluster Deployment

```sql
-- Obtain a global session ID based on the actual scenario
SELECT global_session_id FROM GV$SESSION;

-- Query the tabqueue channel information for the connection session
SELECT SID, SQL_ID, THREAD_ID, STAGE_ID, TYPE FROM TABLE(PX_OBJ(131094));

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

```

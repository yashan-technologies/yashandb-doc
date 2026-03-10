This view is used to query the transmission performance statistics of current session's 1-to-1 channels.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID          | NUMBER     | Group ID                                        |
| GROUP_NODE_ID     | NUMBER     | Node ID within the group                        |
| INST_ID           | NUMBER     | Instance ID                                     |
| TQ_ID             | SMALLINT   | Table queue number                              |
| CHANNEL_ID        | INTEGER    | Channel number                                  |
| DIRECTION         | TINYINT    | Direction, 0 or 1                               |
| SRC_PORT          | SMALLINT   | Sending port number                             |
| DST_PORT          | SMALLINT   | Receiving port number                           |
| SRC_ENDPOINT       | SMALLINT   | Sending node                                    |
| DST_ENDPOINT       | SMALLINT   | Receiving node                                  |
| ACTIVE_TIME       | INTEGER    | Activation time (unit: milliseconds)           |
| SEND_BYTES        | BIGINT     | Amount of data sent during sampling time (unit: byte) |
| SEND_PACKETS      | INTEGER    | Number of packets sent during sampling time     |
| SEND_ACKS         | INTEGER    | Number of ACKs sent during sampling time        |
| SEND_BUFFER_SIZE  | INTEGER    | Size of send buffer during sampling (unit: byte) |
| SEND_WIND         | INTEGER    | Size of send window during sampling, in number of packets |
| CONG_WIND         | INTEGER    | Size of congestion window during sampling, in number of packets |
| SLOW_STARTS       | INTEGER    | Number of slow start restarts during sampling (unit: times) |
| SEND_WIND_LIMITED | INTEGER    | Number of times waited due to send window limits during sampling time |
| CONG_WIND_LIMITED | INTEGER    | Number of times waited due to congestion window limits during sampling time |
| WAIT_SPACE_TIMES  | INTEGER    | Number of times waited due to insufficient send buffer during sampling time |
| WAIT_SPACE_TIMEOUTS | INTEGER  | Number of times waited due to insufficient send buffer timed out during sampling time |
| ROUND_TRIP_TIME   | INTEGER    | Assessment time for processing a round trip message |
| BANDWIDTH         | FLOAT      | Bandwidth (unit: M/s)                          |
| RECV_BYTES        | BIGINT     | Amount of data received during sampling time (unit: byte) |
| RECV_PACKETS      | INTEGER    | Number of packets received during sampling time  |
| RECV_ACKS         | INTEGER    | Number of ACKs received during sampling time     |
| RECV_BUFFER_SIZE  | INTEGER    | Size of receive buffer during sampling (unit: byte) |
| RECV_WIND         | INTEGER    | Size of receive window during sampling, in number of packets |
| WAIT_DATA_TIMES   | INTEGER    | Number of times waited at the receiving end due to data not arriving during sampling time |
| WAIT_DATA_TIMEOUTS | INTEGER   | Number of times waited at the receiving end due to data not arriving timed out during sampling time |
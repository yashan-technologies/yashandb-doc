This view is used to query the transmission performance statistics of current session's 1-to-1 channels.

|Field |Type |Description |
| --- | --- | --- |
| TQ_ID              | SMALLINT   | Table queue number                                                  |
| CHANNEL_ID         | INTEGER    | Channel number                                                      |
| DIRECTION          | TINYINT    | Direction, 0 or 1                                                  |
| SRC_PORT           | SMALLINT   | Source port number                                                  |
| DST_PORT           | SMALLINT   | Destination port number                                             |
| SRC_ENDPOINT       | SMALLINT   | Source node                                                        |
| DST_ENDPOINT       | SMALLINT   | Destination node                                                   |
| ACTIVE_TIME        | INTEGER    | Activation time (unit: milliseconds)                               |
| SEND_BYTES         | BIGINT     | Amount of data sent during the sampling period (unit: byte)       |
| SEND_PACKETS       | INTEGER    | Number of packets sent during the sampling period                  |
| SEND_ACKS          | INTEGER    | Number of acks sent during the sampling period                     |
| SEND_BUFFER_SIZE    | INTEGER    | Size of the send buffer during the sampling (unit: byte)          |
| SEND_WIND          | INTEGER    | Size of the send window during the sampling, unit: number of packets |
| CONG_WIND          | INTEGER    | Size of the congestion window during the sampling, unit: number of packets |
| SLOW_STARTS        | INTEGER    | Number of sliding window restarts during the sampling (unit: times) |
| SEND_WIND_LIMITED  | INTEGER    | Number of times waiting due to send window limitation during the sampling period |
| CONG_WIND_LIMITED  | INTEGER    | Number of times waiting due to congestion window limitation during the sampling period |
| WAIT_SPACE_TIMES   | INTEGER    | Number of times waiting due to insufficient send buffer during the sampling period |
| WAIT_SPACE_TIMEOUTS | INTEGER    | Number of times waiting timed out due to insufficient send buffer during the sampling period |
| ROUND_TRIP_TIME    | INTEGER    | Evaluation time for a round trip of message processing             |
| BANDWIDTH          | FLOAT      | Bandwidth (unit: M/s)                                             |
| RECV_BYTES         | BIGINT     | Amount of data received during the sampling period (unit: byte)   |
| RECV_PACKETS       | INTEGER    | Number of packets received during the sampling period              |
| RECV_ACKS          | INTEGER    | Number of acks received during the sampling period                 |
| RECV_BUFFER_SIZE    | INTEGER    | Size of the receive buffer during the sampling (unit: byte)       |
| RECV_WIND          | INTEGER    | Size of the receive window during the sampling, unit: number of packets |
| WAIT_DATA_TIMES    | INTEGER    | Number of times the receiver waited for data to arrive during the sampling period |
| WAIT_DATA_TIMEOUTS  | INTEGER    | Number of times the receiver waited and timed out due to data not arriving during the sampling period |
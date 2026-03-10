This view is used to query the transmission performance statistics of 1-to-1 channels between nodes in the current session of a distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID | INTEGER | Group ID |
| GROUP_NODE_ID | INTEGER | Node ID within the group |
| TQ_ID | SMALLINT | Table queue number |
| CHANNEL_ID | INTEGER | Channel number |
| DIRECTION | TINYINT | Direction, 0 or 1 |
| SRC_PORT | SMALLINT | Source port number |
| DST_PORT | SMALLINT | Destination port number |
| SRC_ENDPOINT | SMALLINT | Sending node |
| DST_ENDPOINT | SMALLINT | Receiving node |
| ACTIVE_TIME | INTEGER | Activation time (in milliseconds) |
| SEND_BYTES | BIGINT | Amount of data sent during the sampling period (in bytes) |
| SEND_PACKETS | INTEGER | Number of packets sent during the sampling period |
| SEND_ACKS | INTEGER | Number of ACKs sent during the sampling period |
| SEND_BUFFER_SIZE | INTEGER | Size of the sending buffer during sampling (in bytes) |
| SEND_WIND | INTEGER | Size of the sending window during sampling, in number of packets |
| CONG_WIND | INTEGER | Size of the congestion window during sampling, in number of packets |
| SLOW_STARTS | INTEGER | Number of times the sliding window was reset during sampling (in times) |
| SEND_WIND_LIMITED | INTEGER | Number of times waiting due to sending window limitations during the sampling period |
| CONG_WIND_LIMITED | INTEGER | Number of times waiting due to congestion window limitations during the sampling period |
| WAIT_SPACE_TIMES | INTEGER | Number of times waiting due to insufficient sending buffer during the sampling period |
| WAIT_SPACE_TIMEOUTS | INTEGER | Number of times waiting timed out due to insufficient sending buffer during the sampling period |
| ROUND_TRIP_TIME | INTEGER | Assessment time for processing a round trip of messages |
| BANDWIDTH | FLOAT | Bandwidth (in M/s) |
| RECV_BYTES | BIGINT | Amount of data received during the sampling period (in bytes) |
| RECV_PACKETS | INTEGER | Number of packets received during the sampling period |
| RECV_ACKS | INTEGER | Number of ACKs received during the sampling period |
| RECV_BUFFER_SIZE | INTEGER | Size of the receiving buffer during sampling (in bytes) |
| RECV_WIND | INTEGER | Size of the receiving window during sampling, in number of packets |
| WAIT_DATA_TIMES | INTEGER | Number of times the receiver waited due to data not arriving during the sampling period |
| WAIT_DATA_TIMEOUTS | INTEGER | Number of times waiting timed out due to data not arriving during the sampling period |
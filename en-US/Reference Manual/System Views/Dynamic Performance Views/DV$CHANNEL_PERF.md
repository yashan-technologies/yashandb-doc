This view is used to query the transmission performance statistics of 1-to-1 channels between nodes in the current session of an ISC distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP\_ID | INTEGER | Group ID |
| GROUP\_NODE\_ID | INTEGER | Node ID within the group |
| TQ\_ID | SMALLINT | Table queue number |
| CHANNEL\_ID | INTEGER | Channel number |
| DIRECTION | TINYINT | Direction, 0 or 1 |
| SRC\_PORT | SMALLINT | Source port number |
| DST\_PORT | SMALLINT | Destination port number |
| SRC\_ENDPOINT | SMALLINT | Sending node |
| DST\_ENDPOINT | SMALLINT | Receiving node |
| ACTIVE\_TIME | INTEGER | Activation time (in milliseconds) |
| SEND\_BYTES | BIGINT | Amount of data sent during the sampling period (in bytes) |
| SEND\_PACKETS | INTEGER | Number of packets sent during the sampling period |
| SEND\_ACKS | INTEGER | Number of ACKs sent during the sampling period |
| SEND\_BUFFER\_SIZE | INTEGER | Size of the sending buffer during sampling (in bytes) |
| SEND\_WIND | INTEGER | Size of the sending window during sampling, in number of packets |
| CONG\_WIND | INTEGER | Size of the congestion window during sampling, in number of packets |
| SLOW\_STARTS | INTEGER | Number of times the sliding window was reset during sampling (in times) |
| SEND\_WIND\_LIMITED | INTEGER | Number of times waiting due to sending window limitations during the sampling period |
| CONG\_WIND\_LIMITED | INTEGER | Number of times waiting due to congestion window limitations during the sampling period |
| WAIT\_SPACE\_TIMES | INTEGER | Number of times waiting due to insufficient sending buffer during the sampling period |
| WAIT\_SPACE\_TIMEOUTS | INTEGER | Number of times waiting timed out due to insufficient sending buffer during the sampling period |
| ROUND\_TRIP\_TIME | INTEGER | Assessment time for processing a round trip of messages |
| BANDWIDTH | FLOAT | Bandwidth (in M/s) |
| RECV\_BYTES | BIGINT | Amount of data received during the sampling period (in bytes) |
| RECV\_PACKETS | INTEGER | Number of packets received during the sampling period |
| RECV\_ACKS | INTEGER | Number of ACKs received during the sampling period |
| RECV\_BUFFER\_SIZE | INTEGER | Size of the receiving buffer during sampling (in bytes) |
| RECV\_WIND | INTEGER | Size of the receiving window during sampling, in number of packets |
| WAIT\_DATA\_TIMES | INTEGER | Number of times the receiver waited due to data not arriving during the sampling period |
| WAIT\_DATA\_TIMEOUTS | INTEGER | Number of times waiting timed out due to data not arriving during the sampling period |
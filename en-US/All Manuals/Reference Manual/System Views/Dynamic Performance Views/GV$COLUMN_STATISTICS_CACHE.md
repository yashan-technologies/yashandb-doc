This view displays the statistics content on the columns of the dictionary buffer.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID      | NUMBER   | Group ID                                               |
| GROUP_NODE_ID | NUMBER   | Node ID within the group                               |
| INST_ID       | NUMBER   | Instance ID                                           |
| OBJ#          | BIGINT   | ID of the table object or partition object where the column is located |
| COL#          | INTEGER  | Column ID                                              |
| BUCKET_COUNT   | BIGINT   | Number of buckets in the histogram                     |
| ROW_COUNT      | INTEGER  | Number of rows in the histogram                        |
| NULL_COUNT     | BIGINT   | Number of null rows in the column                     |
| MINIMUM       | BIGINT   | Minimum value of bucket count                          |
| MAXIMUM       | BIGINT   | Maximum value of bucket count                          |
| DIST_COUNT    | BIGINT   | Number of distinct values in the column                |
| LOWVAL        | RAW(1000)| Minimum value of the column                            |
| HIGHVAL       | RAW(1000)| Maximum value of the column                            |
| DENSITY       | FLOAT    | Density of the column                                  |
| AVG_LENGTH    | INTEGER  | Average length of the column                           |
| MAX_LENGTH    | INTEGER  | Maximum length of the column                           |
| FLAGS         | INTEGER  | Properties of statistics<br>*   0X01: Indicates whether the statistics were directly specified by the user<br>*   0X02: Indicates whether the statistics of the partition index were obtained through full statistics or estimated from the partition<br>*   0X04: Indicates whether the statistics have been collected<br>*   0X100: Indicates whether the statistics are locked |
| TYPE          | INTEGER  | Histogram type                                        |
| ANALYZE_TIME  | DATE     | Time of the last statistics collection                 |
| SAMPLE_SIZE   | BIGINT   | Number of rows sampled for statistics                  |
| BLOCK_COUNT   | BIGINT   | Number of data blocks for the column                   |
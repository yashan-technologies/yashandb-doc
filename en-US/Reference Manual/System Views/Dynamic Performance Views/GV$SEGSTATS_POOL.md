This view displays detailed statistics of segments in the database.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID       | NUMBER   | Group ID                                      |
| GROUP_NODE_ID  | NUMBER   | Node ID within the group                      |
| INST_ID        | NUMBER   | Instance ID                                   |
|  OBJ#           | BIGINT   | Object OID                                    |
| DATAOBJ#       | BIGINT   | Object DATAOID                                |
| TS#            | INTEGER  | Tablespace ID where the object resides       |
| OBJTYPE        | INTEGER  | Object type                                   |
| OBJNAME        | VARCHAR(64) | Object name                                |
| SUBOBJNAME     | VARCHAR(64) | Subobject name                             |
| TSNAME         | VARCHAR(64) | Tablespace name where the object resides   |
| OWNERNAME      | VARCHAR(64) | Owner name of the object                   |
| LOGIC_READS    | BIGINT   | Number of logical reads       |
| PHYSICS_READS  | BIGINT   | Number of physical reads      |
| BUFFER_BUSY_WAITS | BIGINT | Number of buffer busy waits    |
| XSLOT_WAITS    | BIGINT   | Number of waits due to insufficient data block XSLOT |
| ROW_LOCK_WAITS  | BIGINT   | Number of row lock waits caused by rows locked by other transactions |
| PHYSICS_READ_REQUESTS | BIGINT | Number of physical read requests |
| DB_BLOCK_CHANGES | BIGINT | Number of transactions rolled back during CR block construction |
| GC_CR_BLOCKS_RECEIVED | BIGINT | Number of CR blocks received in cluster  |
| GC_CURRENT_BLOCKS_RECEIVED | BIGINT | Number of current blocks received in cluster  |
| GC_REMOTE_GRANTS  | BIGINT   | Number of remote grant disk reads in cluster |
| GC_BUFFER_BUSY    | BIGINT   | Number of buffer busy waits in cluster |
| SEGMENT_SCANS     | BIGINT   | Number of segment scans        |
| SPACE_ALLOC_SIZE  | BIGINT   | Size of the segment                   |
| DELTA_LOGIC_READS  | BIGINT   | Change in logical reads since snapshot creation  |
| DELTA_PHYSICS_READS | BIGINT | Change in physical reads since snapshot creation  |
| DELTA_BUFFER_BUSY_WAITS | BIGINT | Change in buffer busy waits since snapshot creation  |
| DELTA_XSLOT_WAITS   | BIGINT   | Change in xslot waits since snapshot creation  |
| DELTA_ROW_LOCK_WAITS | BIGINT | Change in row lock waits since snapshot creation  |
| DELTA_PHYSICS_READ_REQUESTS | BIGINT | Change in physical read requests since snapshot creation  |
| DELTA_DB_BLOCK_CHANGES | BIGINT | Change in rolled-back transactions during CR construction since snapshot creation  |
| DELTA_GC_CR_BLOCKS_RECEIVED | BIGINT | Change in CR blocks received in cluster since snapshot creation |
| DELTA_GC_CURRENT_BLOCKS_RECEIVED | BIGINT | Change in current blocks received in cluster since snapshot creation  |
| DELTA_GC_REMOTE_GRANTS  | BIGINT   | Change in remote grant disk reads in cluster since snapshot creation  |
| DELTA_GC_BUFFER_BUSY   | BIGINT   | Change in buffer busy waits in cluster since snapshot creation  |
| DELTA_SEGMENT_SCANS    | BIGINT   | Change in segment scans since snapshot creation |
| DELTA_SPACE_ALLOC_SIZE | BIGINT | Change in segment size since snapshot creation  |

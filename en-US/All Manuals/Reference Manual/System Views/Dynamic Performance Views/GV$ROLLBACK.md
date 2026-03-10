This view provides information about Rollback Segments. A Rollback Segment is a database object used to support the rollback of database transactions and concurrency control.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID       | NUMBER    | Group ID                            |
| GROUP_NODE_ID  | NUMBER    | Node ID within the group           |
| INST_ID        | NUMBER    | Instance ID                         |
| XID            | BIGINT    | Global ID of the transaction       |
| SID            | INTEGER   | Session ID associated with the transaction |
| XRMID          | INTEGER   | XRM ID of the transaction          |
| IN_PRIORITY    | VARCHAR(8)| Whether the transaction is in the priority rollback queue |
| SORT_POS       | INTEGER   | Position of the transaction in the rollback queue |
| RB_POS         | INTEGER   | Current position to roll back in the rollback queue |
| TABLE_LOCK_COUNT| INTEGER  | Number of table locks held by the transaction |
| ROW_LOCK_COUNT | INTEGER   | Number of row locks held by the transaction |
| KEY_LOCK_COUNT | INTEGER   | Number of key locks held by the transaction |
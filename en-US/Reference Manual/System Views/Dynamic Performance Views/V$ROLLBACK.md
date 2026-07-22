|Field |Type |Description |
| --- | --- | --- |
| XID          | BIGINT   | Global ID of the transaction            |
| SID          | INTEGER  | Session ID associated with the transaction |
| XRMID        | INTEGER  | XRM ID of the transaction               |
| IN_PRIORITY  | VARCHAR(8) | Indicates if the transaction is in the priority rollback queue |
| SORT_POS     | INTEGER  | Position of the transaction in the rollback queue |
| RB_POS       | INTEGER  | Current position to which the rollback queue has rolled back |
| TABLE_LOCK_COUNT | INTEGER | Number of table locks held by the transaction |
| ROW_LOCK_COUNT   | INTEGER | Number of row locks held by the transaction |
| KEY_LOCK_COUNT   | INTEGER | Number of key locks held by the transaction |
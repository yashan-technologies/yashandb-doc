This view displays information about all current locks.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID      | NUMBER   | Group ID     |
| GROUP_NODE_ID | NUMBER   | Node ID within the group |
| INST_ID       | NUMBER   | Instance ID  |
| SID           | SMALLINT | Session ID   |
| ID1           | BIGINT   | Lock Identifier<br/>1. If the lock is held by the session<br/>* When the lock type is a table lock, ID1 records the table's ID<br/>* When the lock type is a row-table lock/key lock/list row lock, ID1 records the page ID of the row/index key/row in the list<br/>2. If the lock is being waited on by the session<br/>* When the waiting lock type is a table lock, ID1 records the table's ID<br/>* When the waiting lock type is a row lock/key lock/list row lock, ID1 records the transaction ID holding the row lock/key lock/list row lock |
| ID2           | BIGINT   | Lock Identifier<br/>* If the session holds a row lock, ID2 records the corresponding Xslot ID of the row<br/>* In other cases, ID2 is null |
| LMODE         | VARCHAR(32) | Lock Type held by the session<br/>* TS: Shared table lock<br/>* TX: Exclusive table lock<br/>* ROW: Row lock<br/>* KEY: Key value lock<br/>* SLICE_S: LSC table slice shared lock<br/>* SLICE_X: LSC table slice exclusive lock<br/>* UL_S_MODE: User shared lock<br/>* UL_X_MODE: User exclusive lock |
| REQUEST       | VARCHAR(32) | Lock Type requested by the session<br/>* TS: Shared table lock<br/>* TX: Exclusive table lock<br/>* ROW: Row lock<br/>* KEY: Key value lock<br/>* SLICE_S: LSC table slice shared lock<br/>* SLICE_X: LSC table slice exclusive lock<br/>* UL_S_MODE: User shared lock<br/>* UL_X_MODE: User exclusive lock |
This view displays the lock information for all nodes in a distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID       | INTEGER    | Group ID                                                                         |
| GROUP_NODE_ID  | INTEGER    | Node ID within the group                                                         |
| SID            | SMALLINT   | Session ID                                                                       |
| ID1            | BIGINT     | Lock Identifier <br> 1. If it is a lock held by a session <br> When the lock type is a table lock, ID1 records the ID of the table <br> When the lock type is a row lock/key lock/list row lock, ID1 records the page ID where the row/index key/list row is located <br> 2. If it is a lock that the session is waiting for <br> When the waiting lock type is a table lock, ID1 records the ID of the table <br> When the waiting lock type is a row lock/key lock/list row lock, ID1 records the transaction ID holding that row lock/key lock/list row lock |
| ID2            | BIGINT     | Lock Identifier <br> * If it is a row lock held by a session, ID2 records the corresponding Xslot ID of the row <br> * In other cases, ID2 is null |
| LMODE          | VARCHAR(32)| Type of lock held by the session <br> * TS: Shared Table Lock <br> * TX: Exclusive Table Lock <br> * ROW: Row Lock <br> * KEY: Key Value Lock <br> * SLICE_S: LSC Table Slice Shared Lock <br> * SLICE_X: LSC Table Slice Exclusive Lock |
| REQUEST        | VARCHAR(32)| Type of lock being waited for by the session <br> * TS: Shared Table Lock <br> * TX: Exclusive Table Lock <br> * ROW: Row Lock <br> * KEY: Key Value Lock <br> * SLICE_S: LSC Table Slice Shared Lock <br> * SLICE_X: LSC Table Slice Exclusive Lock |
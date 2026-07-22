This view shows information about all current locks.

|Field |Type |Description |
| --- | --- | --- |
| SID | SMALLINT | Session ID |
| ID1 | BIGINT | Lock identifier<br/>1. If the lock is held by the session<br/>* When the lock type is table lock, ID1 records the ID of the table<br/>* When the lock type is row lock/key lock/list lock, ID1 records the page ID where the row/index key/list entry is located<br/>2. If the lock is being waited on by the session<br/>* When the waiting lock type is table lock, ID1 records the ID of the table<br/>* When the waiting lock type is row lock/key lock/list lock, ID1 records the transaction ID that holds the row lock/key lock/list lock |
| ID2 | BIGINT | Lock identifier<br/>* If the session holds a row lock, ID2 records the corresponding Xslot ID of that row<br/>* In other cases, ID2 is null |
| LMODE | VARCHAR(32) | Lock type held by the session<br/>* TS: Shared table lock<br/>* TX: Exclusive table lock<br/>* ROW: Row lock<br/>* KEY: Key lock<br/>* SLICE\_S: LSC table slice shared lock<br/>* SLICE\_X: LSC table slice exclusive lock<br/>* UL_S_MODE: User shared lock<br/>* UL_X_MODE: User exclusive lock |
| REQUEST | VARCHAR(32) | Lock type requested by the session<br/>* TS: Shared table lock<br/>* TX: Exclusive table lock<br/>* ROW: Row lock<br/>* KEY: Key lock<br/>* SLICE\_S: LSC table slice shared lock<br/>* SLICE\_X: LSC table slice exclusive lock<br/>* UL_S_MODE: User shared lock<br/>* UL_X_MODE: User exclusive lock |
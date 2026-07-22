This view displays information about pending transactions in all nodes of an ISC distributed cluster and their status.

|Field |Type |Description |
|-------------------------| --- |-------------------------------------------------------------------------------------------------------------------------------------------------|
| GROUP\_ID               | INTEGER | Group ID                                                                                                                                         |
| GROUP\_NODE\_ID         | INTEGER | Node ID within the group                                                                                                                         |
| GLOBAL\_TRAN\_ID        | BIGINT  | GTID (Global Transaction ID) of the XA transaction                                                                                              |
| LOCAL\_SESSION\_ID      | INTEGER | Local session ID of the XA transaction                                                                                                          |
| GLOBAL\_SESSION\_ID     | INTEGER | Distributed session ID of the XA transaction                                                                                                     |
| SESSION\_SERIAL         | INTEGER | Version number of the distributed session to which the XA transaction belongs                                                                     |
| STATE                   | VARCHAR(16) | XA transaction state<br>\*   IDLE<br>\*   OPEN<br>\*   PREPARED<br>\*   COMMIT<br>\*   ROLLBACKING<br>\*   ROLLBACK<br>\*   COMMIT FORCE<br>\*   ROLLBACK FORCE |
| SCN                     | BIGINT  | End SCN of the pending transaction                                                                                                               |
| TRAN\_COMMENT           | VARCHAR(256) | Transaction comments                                                                                                                              |
| PENDING\_TIME           | DATE    | Time the XA transaction entered the pending state; this field becomes invalid if the database is restarted                                         |
| RETRY\_TIME             | DATE    | Time the XA transaction in phase 1 re-enters the pending state after the database is restarted                                                   |
| NODE\_LIST               | VARCHAR(8000) | Information about distributed transaction nodes                                                                                                     |
| IS_DSTB_SESSION_UNBIND  | VARCHAR(8) | Whether the distributed transaction has been unbound from the distributed session                                                                 |
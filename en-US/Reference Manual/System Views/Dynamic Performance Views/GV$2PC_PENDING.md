This view displays information related to pending transactions and their status.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID           | NUMBER      | Group ID                        |
| GROUP_NODE_ID      | NUMBER      | Node ID within the group       |
| INST_ID            | NUMBER      | Instance ID                     |
| GLOBAL_TRAN_ID     | RAW(128)    | GTID (Global Transaction ID) of the XA transaction |
| LOCAL_SESSION_ID    | INTEGER     | Local session ID of the XA transaction |
| GLOBAL_SESSION_ID   | INTEGER     | Distributed session ID of the XA transaction |
| SESSION_SERIAL      | INTEGER     | Version number of the distributed session of the XA transaction |
| STATE              | VARCHAR(16) | XA transaction status<br>\*   IDLE<br>\*   OPEN<br>\*   PREPARED<br>\*   COMMIT<br>\*   ROLLBACKING<br>\*   ROLLBACK<br>\*   COMMIT FORCE<br>\*   ROLLBACK FORCE |
| SCN                | BIGINT      | Ending SCN of the pending transaction |
| TRAN_COMMENT       | VARCHAR(256) | Transaction remarks              |
| PENDING_TIME       | DATE        | Time when the XA transaction entered the pending state; this field is invalid after database restart |
| RETRY_TIME         | DATE        | Time when the XA transaction in phase1 re-entered pending after database restart |
| NODE_LIST          | VARCHAR(8000) | Information of distributed transaction nodes |
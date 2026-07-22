This view shows information about pending transactions and their status.

|Field |Type |Description |
| --- | --- | --- |
| GLOBAL\_TRAN\_ID    | RAW(128)  | GTID (Global Transaction Identifier) of the XA transaction |
| LOCAL\_SESSION\_ID  | INTEGER   | Local session ID of the XA transaction            |
| GLOBAL\_SESSION\_ID | INTEGER   | Distributed session ID of the XA transaction      |
| SESSION\_SERIAL     | INTEGER   | Version number of the distributed session of the XA transaction |
| STATE                | VARCHAR(16) | Status of the XA transaction<br>\*   IDLE<br>\*   OPEN<br>\*   PREPARED<br>\*   COMMIT<br>\*   ROLLBACKING<br>\*   ROLLBACK<br>\*   COMMIT FORCE<br>\*   ROLLBACK FORCE |
| SCN                  | BIGINT    | End SCN of the pending transaction                |
| TRAN\_COMMENT        | VARCHAR(256) | Transaction remarks                               |
| PENDING\_TIME       | DATE      | Time at which the XA transaction entered pending state; this field becomes invalid after a database restart |
| RETRY\_TIME         | DATE      | Time when the XA transaction in phase1 re-enters pending state after a database restart |
| NODE\_LIST          | VARCHAR(8000) | Information about distributed transaction nodes   |
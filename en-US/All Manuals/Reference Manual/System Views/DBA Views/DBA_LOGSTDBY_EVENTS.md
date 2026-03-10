This view is applicable only to logical standby databases, used to record the failures and related information that occur when the redo logs are applied to the logical standby database.

|Field |Type |Description |
| --- | --- | --- |
| EVENT_TIME   | TIMESTAMP(6)  | Records the SCN of the event.                                             |
| START_SCN    | BIGINT     | The SCN at which the transaction started on the primary database. This SCN refers to the SCN on the primary database. |
| CURRENT_SCN  | BIGINT     | The SCN that has currently been applied, related to the primary database's SCN. If a failure occurs, check this column to determine which archived log file contains the source of the failure (e.g., unsupported records). |
| COMMIT_SCN   | BIGINT     | The SCN value of the changes committed on the primary database.           |
| XID          | INTEGER    | The transaction ID related to the primary database.                       |
| EVENT        | CLOB       | The statement being processed when the failure occurred.                   |
| STATUS_CODE  | INTEGER    | The error code number of YashanDB.                                        |
| STATUS       | VARCHAR(2048)| Describes the current error or status.                                    |
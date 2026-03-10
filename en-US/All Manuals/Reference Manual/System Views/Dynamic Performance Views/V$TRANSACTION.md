This view shows transaction summary information.

|Field |Type |Description |
| --- | --- | --- |
| XID           | BIGINT    | Global ID of the transaction                                                  |
| SID           | INTEGER   | Session ID associated with the transaction                                    |
| XRMID         | INTEGER   | XRM ID of the transaction                                                    |
| XEXT          | INTEGER   | Number of the transaction area corresponding to the current transaction      |
| XNODE         | INTEGER   | Number of the current transaction within the corresponding transaction area   |
| XSN           | INTEGER   | Version number of the current transaction                                     |
| STATUS        | VARCHAR(8)| Transaction status<br>\*   IDLE<br>\*   OPEN<br>\*   PHASE1<br>\*   END    |
| RESIDUAL      | VARCHAR(8)| Whether the transaction is waiting for rollback in the background thread<br>\*   TRUE<br>\*   FALSE |
| USED_UBLK    | INTEGER   | Number of undo blocks used by the transaction                                 |
| FIRST_UBAFIL | INTEGER   | Number of the first undo block file used by the transaction                  |
| FIRST_UBABLK | INTEGER   | ID of the first undo block used by the transaction                           |
| FIRST_UBAVER | INTEGER   | Version number of the first undo block used by the transaction               |
| FIRST_UBAREC | INTEGER   | Number of the first undo record after the transaction started                |
| LAST_UBAFIL  | INTEGER   | Number of the last undo block file used by the transaction                   |
| LAST_UBABLK  | INTEGER   | ID of the last undo block used by the transaction                            |
| PRV_XID      | BIGINT    | Previous transaction ID, normally empty<br/>If this transaction is an autonomous transaction, this field contains the ID of the previous transaction |
| PTX_XID      | BIGINT    | Parent transaction ID, empty if the current transaction is not a parallel transaction<br/>If the current transaction is a parallel transaction:<br>\*   If the current transaction is the parent, this field contains the ID of the current transaction<br>\*   If the current transaction is a child, this field contains the ID of the parent transaction |
| START_DATE    | DATE      | Transaction start time                                                        |
| ISOLATION_LEVEL| VARCHAR(32)| Isolation level                                                              |
| START_SCN    | BIGINT    | SCN at the time the transaction started                                       |
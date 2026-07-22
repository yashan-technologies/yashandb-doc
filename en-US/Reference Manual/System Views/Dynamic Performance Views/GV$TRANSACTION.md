This view displays transaction summary information.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID         | NUMBER  | Group ID                                                    |
| GROUP_NODE_ID    | NUMBER  | Node ID within the group                                    |
| INST_ID          | NUMBER  | Instance ID                                                |
| XID              | BIGINT  | Global ID of the transaction                                |
| SID              | INTEGER | Session ID associated with the transaction                  |
| XRMID            | INTEGER | XRM ID of the transaction                                   |
| XEXT             | INTEGER | Transaction zone number corresponding to the current transaction |
| XNODE            | INTEGER | Number of the current transaction within the corresponding transaction zone |
| XSN              | INTEGER | Version number of the current transaction                   |
| STATUS           | VARCHAR(8) | Transaction status<br>*   IDLE<br>*   OPEN<br>*   PHASE1<br>*   END |
| RESIDUAL         | VARCHAR(8) | Whether the transaction is waiting for rollback in the background thread<br>*   TRUE<br>*   FALSE |
| USED_UBLK       | INTEGER | Number of undo blocks used by the transaction               |
| FIRST_UBAFIL    | INTEGER | File number of the first undo block used by the transaction |
| FIRST_UBABLK    | INTEGER | ID of the first undo block used by the transaction          |
| FIRST_UBAVER     | INTEGER | Version number of the first undo block used by the transaction |
| FIRST_UBAREC    | INTEGER | Number of the first undo record after the transaction was started |
| LAST_UBAFIL     | INTEGER | File number of the last undo block of the transaction      |
| LAST_UBABLK     | INTEGER | ID of the last undo block of the transaction                |
| PRV_XID         | BIGINT  | Previous transaction ID, normally empty<br>If this transaction is an autonomous transaction, this field contains the ID of the previous transaction |
| PTX_XID         | BIGINT  | Parent transaction ID, empty when the current transaction is not a parallel transaction<br>If the current transaction is a parallel transaction:<br>*   If the current transaction is the parent transaction, this field contains the current transaction ID<br>*   If the current transaction is the child transaction, this field contains the parent transaction ID |
| START_DATE      | DATE    | Transaction start time                                      |
| ISOLATION_LEVEL  | VARCHAR(32) | Isolation level                                           |
| START_SCN       | BIGINT  | SCN at the start of the transaction                        |
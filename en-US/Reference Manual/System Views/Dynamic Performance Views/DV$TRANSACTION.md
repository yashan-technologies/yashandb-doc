This view shows a summary of transaction information for all nodes in a distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID         | INTEGER   | Group ID                                                                       |
| GROUP_NODE_ID    | INTEGER   | Node ID within the group                                                        |
| XID              | BIGINT    | Global ID of the transaction                                                    |
| SID              | INTEGER   | Session ID that this transaction is bound to                                   |
| XRMID            | INTEGER   | XRM ID of the transaction                                                       |
| XEXT             | INTEGER   | Transaction area number corresponding to the current transaction               |
| XNODE            | INTEGER   | Number of the current transaction within the corresponding transaction area     |
| XSN              | INTEGER   | Version number of the current transaction                                       |
| STATUS           | VARCHAR(8)| Transaction status <br/> * IDLE <br/> * OPEN <br/> * PHASE1 <br/> * END      |
| RESIDUAL         | VARCHAR(8)| Whether the transaction is waiting for rollback in the background thread <br/> * TRUE <br/> * FALSE |
| USED_UBLK       | INTEGER   | Number of undo blocks used by the transaction                                   |
| FIRST_UBAFIL    | INTEGER   | File number of the first undo block used by the transaction                    |
| FIRST_UBABLK     | INTEGER   | ID of the first undo block used by the transaction                              |
| FIRST_UBAVER     | INTEGER   | Version number of the first undo block used by the transaction                 |
| FIRST_UBAREC     | INTEGER   | Number of the first undo record after the transaction is opened                |
| LAST_UBAFIL      | INTEGER   | File number of the last undo block used by the transaction                     |
| LAST_UBABLK      | INTEGER   | ID of the last undo block used by the transaction                               |
| PRV_XID          | BIGINT    | Previous transaction ID, normally empty <br/> If this transaction is autonomous, this field will have the previous transaction ID |
| PTX_XID          | BIGINT    | Parent transaction ID, empty when the current transaction is not parallel <br/> If the current transaction is parallel: <br/> * If the current transaction is the parent, this field will have the current transaction ID <br/> * If the current transaction is a child, this field will have the parent transaction ID |
| START_DATE       | DATE      | Date and time when the transaction started                                      |
| ISOLATION_LEVEL   | VARCHAR(32)| Isolation level                                                                |
| START_SCN        | BIGINT    | SCN when the transaction started                                                |
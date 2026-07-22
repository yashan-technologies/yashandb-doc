This view displays a list of node information registered in MN.

|Field |Type |Description |
| --- | --- | --- |
| GROUP\_ID           | INTEGER   | Group ID                                                                    |
| GROUP\_NODE\_ID     | INTEGER   | Node ID within the group                                                    |
| TYPE                | VARCHAR(16) | Node type, such as CN, DN, etc.                                            |
| ENDPOINT            | SMALLINT  | Internal network communication endpoint                                      |
| TERM                | BIGINT    | Node term                                                                   |
| ROLE                | VARCHAR(16) | Node role, such as Primary, Standby                                        |
| VERSION             | INTEGER   | Node static information version number                                       |
| STATE               | VARCHAR(16) | Node static state, such as INVALID (invalid value), INIT (initialization), REMOVED (deleted), STARTED (started), STOPPED (stopped), ISOLATED (isolated). |
| STATUS\_VERSION     | BIGINT    | Node dynamic information version number                                      |
| RUNNING\_STATE      | VARCHAR(32) | Node dynamic state, such as INVALID (invalid value), NORMAL (normal), SUSPECT (suspected), ABNORMAL (abnormal), FULL SYNC (synchronizing). |
| HOST                | VARCHAR(256) | Node server address                                                         |
| DATA\_PATH          | VARCHAR(256) | Node data storage directory                                                 |
| LISTEN\_ADDR        | VARCHAR(128) | Node service address                                                         |
| REPLICA\_ADDR       | VARCHAR(128) | Node replication link address                                               |
| DIN\_ADDR           | VARCHAR(128) | Distributed internal communication address                                   |
| CREATE\_TIME        | TIMESTAMP(6) | Node creation time                                                           |
| LAST\_UPDATE\_TIME  | TIMESTAMP(6) | Last update time of node static information                                  |
| STATUS\_UPDATE\_TIME | TIMESTAMP(6) | Last update time of node dynamic information                                  |
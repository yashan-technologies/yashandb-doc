This view displays the node information of the node itself.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID        | NUMBER      | Group ID                                            |
| GROUP_NODE_ID   | NUMBER      | Node ID within the group                            |
| INST_ID         | NUMBER      | Instance ID                                        |
| TYPE            | VARCHAR(16) | Node type, such as CN, DN, etc.                    |
| ENDPOINT        | SMALLINT    | Internal network communication endpoint             |
| TERM            | BIGINT      | Node term                                          |
| ROLE            | VARCHAR(16) | Node role, such as Primary, Standby                |
| VERSION         | INTEGER     | Node static information version number              |
| STATE           | VARCHAR(16) | Node static state, such as INVALID (invalid value), INIT (initialization), REMOVED (deleted), STARTED (started), STOPPED (stopped), ISOLATED (isolated) |
| STATUS_VERSION   | BIGINT      | Node dynamic information version number             |
| RUNNING_STATE    | VARCHAR(32) | Node dynamic state, such as INVALID (invalid value), NORMAL (normal), SUSPECT (suspected), ABNORMAL (abnormal), FULL SYNC (synchronized) |
| HOST            | VARCHAR(256) | Node server address                                 |
| DATA_PATH       | VARCHAR(256) | Node data storage directory                          |
| LISTEN_ADDR     | VARCHAR(128) | Node service address                                 |
| REPLICA_ADDR    | VARCHAR(128) | Node replication link address                        |
| DIN_ADDR        | VARCHAR(128) | Distributed internal communication address          |
| CREATE_TIME     | TIMESTAMP(6)   | Node creation time                                  |
| LAST_UPDATE_TIME | TIMESTAMP(6)   | Last update time of node static information         |
| STATUS_UPDATE_TIME | TIMESTAMP(6) | Last update time of node dynamic information        |
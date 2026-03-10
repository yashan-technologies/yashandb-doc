This view displays the list of NODE INFO information stored in the CM module.

|Field |Type |Description |
| --- | --- | --- |
| TYPE | VARCHAR(16) | Node type, e.g., CN, DN, etc. |
| GROUP_ID | INTEGER | Group ID |
| GROUP_NODE_ID | INTEGER | Node ID within the group |
| ENDPOINT | SMALLINT | Internal network communication endpoint |
| TERM | BIGINT | Node term |
| ROLE | VARCHAR(16) | Node role, e.g., Primary, Secondary |
| VERSION | INTEGER | Node static information version number |
| STATE | VARCHAR(16) | Node static status, e.g., INVALID (invalid value), INIT (initialization), REMOVED (deleted), STARTED (started), STOPPED (stopped), ISOLATED (isolated) |
| STATUS_VERSION | BIGINT | Node dynamic information version number |
| RUNNING_STATE | VARCHAR(32) | Node dynamic status, e.g., INVALID (invalid value), NORMAL (normal), SUSPECT (suspect), ABNORMAL (abnormal), FULL SYNC (synchronized) |
| HOST | VARCHAR(256) | Node server address |
| DATA_PATH | VARCHAR(256) | Node data storage directory |
| LISTEN_ADDR | VARCHAR(128) | Node service address |
| REPLICA_ADDR | VARCHAR(128) | Node replication link address |
| DIN_ADDR | VARCHAR(128) | Node data plane address |
| CREATE_TIME | TIMESTAMP(6) | Node creation time |
| LAST_UPDATE_TIME | TIMESTAMP(6) | Last update time of the node static information |
| STATUS_UPDATE_TIME | TIMESTAMP(6) | Last update time of the node dynamic information |
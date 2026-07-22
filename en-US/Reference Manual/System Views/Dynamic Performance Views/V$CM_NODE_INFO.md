This view displays the list of NODE INFO information stored in the CM module.

|Field |Type |Description |
| --- | --- | --- |
| TYPE | VARCHAR(16) | Node type, e.g., CN, DN, etc. |
| GROUP\_ID | INTEGER | Group ID |
| GROUP\_NODE\_ID | INTEGER | Node ID within the group |
| ENDPOINT | SMALLINT | Internal network communication endpoint |
| TERM | BIGINT | Node term |
| ROLE | VARCHAR(16) | Node role, e.g., Primary, Secondary |
| VERSION | INTEGER | Node static information version number |
| STATE | VARCHAR(16) | Node static status, e.g., INVALID (invalid value), INIT (initialization), REMOVED (deleted), STARTED (started), STOPPED (stopped), ISOLATED (isolated) |
| STATUS\_VERSION | BIGINT | Node dynamic information version number |
| RUNNING\_STATE | VARCHAR(32) | Node dynamic status, e.g., INVALID (invalid value), NORMAL (normal), SUSPECT (suspect), ABNORMAL (abnormal), FULL SYNC (synchronized) |
| HOST | VARCHAR(256) | Node server address |
| DATA\_PATH | VARCHAR(256) | Node data storage directory |
| LISTEN\_ADDR | VARCHAR(128) | Node service address |
| REPLICA\_ADDR | VARCHAR(128) | Node replication link address |
| DIN\_ADDR | VARCHAR(128) | Node data plane address |
| CREATE\_TIME | TIMESTAMP(6) | Node creation time |
| LAST\_UPDATE\_TIME | TIMESTAMP(6) | Last update time of the node static information |
| STATUS\_UPDATE\_TIME | TIMESTAMP(6) | Last update time of the node dynamic information |
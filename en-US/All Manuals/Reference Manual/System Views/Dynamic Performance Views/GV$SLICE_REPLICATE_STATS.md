In Standalone Deployment, this view shows the synchronization status of all local stable data.

In ISC Distributed Cluster Deployment, this view shows the synchronization status of all local stable data for the current instance, except for CN; on CN, this view shows the synchronization status of all local stable data for the MN instance.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID      | NUMBER    | Group ID                        |
| GROUP_NODE_ID | NUMBER    | Node ID within the group       |
| INST_ID       | NUMBER    | Instance ID                     |
| DEST_ID       | INTEGER   | Standby database ID, corresponds to ARCHIVE_DEST_x parameter |
| BUCKET_NAME   | VARCHAR(255) | Path of the databucket where the synchronized data is located |
| DATAOBJ       | BIGINT    | Object ID of the synchronized data |
| SLICE_ID      | BIGINT    | Synchronized data file number   |
| PROGRESS      | INTEGER   | Synchronization progress, range [0,100] |
| SPEED         | INTEGER   | Synchronization speed (unit: MB/s) |
| EXEC_ROUND    | INTEGER   | Number of attempts to synchronize the current data |
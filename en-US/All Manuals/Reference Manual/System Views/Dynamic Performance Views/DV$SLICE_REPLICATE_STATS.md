In ISC Distributed Cluster Deployment, this view shows the synchronization status of all local stable data for the current instance, except for the CN; on the CN, this view displays the synchronization status of all local stable data for the MN instance.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID       | INTEGER  | Group ID                                     |
| GROUP_NODE_ID  | INTEGER  | Node ID within the group                     |
| DEST_ID        | INTEGER  | Standby database ID, corresponding to ARCHIVE_DEST_x parameter |
| BUCKET_NAME    | VARCHAR(255) | Path of the databucket where the synchronized data is located |
| DATAOBJ        | BIGINT   | Object ID of the synchronized data           |
| SLICE_ID       | BIGINT   | Synchronization data file number             |
| PROGRESS       | INTEGER  | Synchronization progress, range [0,100]     |
| SPEED          | INTEGER  | Synchronization speed (unit: MB/s)          |
| EXEC_ROUND     | INTEGER  | Current number of attempts to synchronize data |
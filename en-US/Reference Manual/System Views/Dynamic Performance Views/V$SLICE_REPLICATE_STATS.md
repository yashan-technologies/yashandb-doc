This view displays the synchronization status of all local stable data.

|Field |Type |Description |
| --- | --- | --- |
| DEST_ID  | INTEGER | Standby database ID, corresponding to the ARCHIVE_DEST_x parameter |
| BUCKET_NAME | VARCHAR(255) | Path of the data bucket where the synchronized data is located |
| DATAOBJ | BIGINT | ID of the object to which the synchronized data belongs |
| SLICE_ID  | BIGINT | Number of the synchronized data file |
| PROGRESS | INTEGER | Synchronization progress, range [0,100] |
| SPEED | INTEGER | Synchronization speed (unit: MB/s) |
| EXEC_ROUND | INTEGER | Number of attempts made to synchronize the current data |
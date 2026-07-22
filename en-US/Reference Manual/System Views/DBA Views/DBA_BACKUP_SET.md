This view displays information about all backup sets in the system.

|Field |Type |Description |
| --- | --- | --- |
| RECID#                | BIGINT     | Backup set number                                                                          |
| START\_TIME           | DATE       | Start time, derived from internal SCN, may differ from actual machine time                |
| COMPLETION\_TIME      | DATE       | Completion time, derived from internal SCN, may differ from actual machine time           |
| TYPE                  | VARCHAR(11)| Backup type<br>\* FULL: Full backup <br>\* INCREMENTAL: Incremental backup <br>\* ARCHIVE: Archive backup <br>\* TABLESPACE: Tablespace backup |
| INCREMENT\_LEVEL      | INTEGER    | Incremental backup level<br>\* 0: All data backed up <br>\* 1: Indicates only incremental data backed up based on a certain incremental backup set |
| INCREMENT\_ID#        | INTEGER    | Serial number within the incremental backup set                                             |
| PATH                  | VARCHAR(256)| Path of the backup set                                                                     |
| TAG                   | VARCHAR(64)| Unique tag                                                                                  |
| TRUNC\_LSN            | BIGINT     | Baseline log sequence number of the backup set; redo logs prior to this sequence number do not need to be applied |
| COMPRESS\_LEVEL       | VARCHAR(6) | Compression level                                                                           |
| INPUT\_BYTES          | BIGINT     | Total bytes read during backup process                                                      |
| OUTPUT\_BYTES         | BIGINT     | Total bytes flushed during backup process                                                   |
| RCY\_BEGIN\_ASN       | INTEGER    | ASN corresponding to the starting point of backup set apply: archive sequence number, to be the smallest ASN among all nodes in the cluster |
| RCY\_BEGIN\_LFN       | BIGINT     | LFN corresponding to the starting point of backup set apply: log sequence number           |
| RCY\_END\_ASN         | INTEGER    | ASN corresponding to the ending point of backup set apply: archive sequence number, to be the largest ASN among all nodes in the cluster |
| RCY\_END\_LFN         | BIGINT     | LFN corresponding to the ending point of backup set apply: log sequence number             |
| COMPRESS\_ALGO        | VARCHAR(4) | Compression algorithm                                                                       |
| ENCRYPT\_ALGO         | VARCHAR(6) | Encryption algorithm                                                                        |
| DEFAULT\_BASE         | BOOLEAN    | Default baseline, applicable only to incremental backups                                      |
| RESTORE\_TIME         | DATE       | The time of the last restore of the database when this backup set was generated; displays as empty if the DB has never executed a RESTORE operation |
| CHECKPOINT\_TIME      | TIMESTAMP(6)  | Timestamp for database consistency recovery; if it displays as empty, this database is an old version upgraded to a new version, and this item does not need to be referenced |
| CHECKPOINT\_SCN       | BIGINT     | SCN for database consistency recovery                                                        |
| SPC\_IMPORT\_SCN      | BIGINT     | If there are tablespaces created through migration in the current node, this SCN is updated to the maximum SCN of all newly migrated tablespaces |
| SPC\_IMPORT\_LSN      | BIGINT     | If there are tablespaces created through migration in the current node, this LSN is updated to the maximum LSN of all newly migrated tablespaces |
| BS\_KEY  | BIGINT     | Unique identifier for the current backup set        |
| BS\_STATUS| VARCHAR(10) | Current status of the backup set. <br>\* AVAILABLE: The backup set is complete and usable. <br>\* INCOMPLETE: The backup set is partially completed. (In multitenant deployments, this occurs when not all PDBs were successfully backed up) |
| CON\_ID | BIGINT |This field indicates the ID of the container (the CDB root or PDB) to which the backup set belongs. |
| CON\_UID | VARCHAR(64) | This field indicates the UID of the container (the CDB root or PDB) to which the backup set belongs.|                                          |
This view displays information about all backup sets in the system.

|Field |Type |Description |
| --- | --- | --- |
| RECID#                | BIGINT     | Backup set number                                                                          |
| START_TIME           | DATE       | Start time, derived from internal SCN, may differ from actual machine time                |
| COMPLETION_TIME      | DATE       | Completion time, derived from internal SCN, may differ from actual machine time           |
| TYPE                  | VARCHAR(11)| Backup type<br>\* FULL: Full backup <br>\* INCREMENTAL: Incremental backup <br>\* ARCHIVE: Archive backup <br>\* TABLESPACE: Tablespace backup |
| INCREMENT_LEVEL      | INTEGER    | Incremental backup level<br>\* 0: All data backed up <br>\* 1: Indicates only incremental data backed up based on a certain incremental backup set |
| INCREMENT_ID#        | INTEGER    | Serial number within the incremental backup set                                             |
| PATH                  | VARCHAR(256)| Path of the backup set                                                                     |
| TAG                   | VARCHAR(64)| Unique tag                                                                                  |
| TRUNC_LSN            | BIGINT     | Baseline log sequence number of the backup set; redo logs prior to this sequence number do not need to be applied |
| COMPRESS_LEVEL       | VARCHAR(6) | Compression level                                                                           |
| INPUT_BYTES          | BIGINT     | Total bytes read during backup process                                                      |
| OUTPUT_BYTES         | BIGINT     | Total bytes flushed during backup process                                                   |
| RCY_BEGIN_ASN       | INTEGER    | ASN corresponding to the starting point of backup set apply: archive sequence number, to be the smallest ASN among all nodes in the cluster |
| RCY_BEGIN_LFN       | BIGINT     | LFN corresponding to the starting point of backup set apply: log sequence number           |
| RCY_END_ASN         | INTEGER    | ASN corresponding to the ending point of backup set apply: archive sequence number, to be the largest ASN among all nodes in the cluster |
| RCY_END_LFN         | BIGINT     | LFN corresponding to the ending point of backup set apply: log sequence number             |
| COMPRESS_ALGO        | VARCHAR(4) | Compression algorithm                                                                       |
| ENCRYPT_ALGO         | VARCHAR(6) | Encryption algorithm                                                                        |
| DEFAULT_BASE         | BOOLEAN    | Default baseline, applicable only to incremental backups                                      |
| RESTORE_TIME         | DATE       | The time of the last restore of the database when this backup set was generated; displays as empty if the DB has never executed a RESTORE operation |
| CHECKPOINT_TIME      | TIMESTAMP(6)  | Timestamp for database consistency recovery; if it displays as empty, this database is an old version upgraded to a new version, and this item does not need to be referenced |
| CHECKPOINT_SCN       | BIGINT     | SCN for database consistency recovery                                                        |
| SPC_IMPORT_SCN      | BIGINT     | If there are tablespaces created through migration in the current node, this SCN is updated to the maximum SCN of all newly migrated tablespaces |
| SPC_IMPORT_LSN      | BIGINT     | If there are tablespaces created through migration in the current node, this LSN is updated to the maximum LSN of all newly migrated tablespaces |
| BS_KEY               | BIGINT     | Unique identifier for the current backup set                                               |
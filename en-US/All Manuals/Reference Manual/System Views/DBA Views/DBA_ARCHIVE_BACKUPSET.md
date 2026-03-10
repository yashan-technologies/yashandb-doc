This view displays information about all archived log file backup sets in the system.

|Field |Type |Description |
| --- | --- | --- |
| RECID# | BIGINT  | Backup set number |
| INSTANCE_NUMBER# | INTEGER      | Instance number |
| START_TIME | DATE | Start time, derived from the internal SCN, may have discrepancies with actual machine time |
| COMPLETION_TIME | DATE | Completion time, derived from the internal SCN, may have discrepancies with actual machine time |
| TYPE  | VARCHAR(11) | Backup type<br>* FULL: Full backup <br>* INCREMENTAL: Incremental backup<br>* ARCHIVE: Archive backup |
| PATH  | VARCHAR(256) | Backup set path |
| TAG   | VARCHAR(64) | Unique label |
| COMPRESS_ALGO  | VARCHAR(4) | Compression algorithm |
| COMPRESS_LEVEL | VARCHAR(6) | Compression level |
| ENCRYPT_ALGO   | VARCHAR(6) | Encryption algorithm |
| INPUT_BYTES    | BIGINT  | Total bytes read during the backup process |
| OUTPUT_BYTES   | BIGINT  | Total bytes written to disk during the backup process |
| SEQUENCE_BEGIN# | INTEGER | Minimum ASN of archived files in the backup set |
| SEQUENCE_END# | INTEGER  | Maximum ASN of archived files in the backup set |
| MIN_FIRST_CHANGE#   | BIGINT | Minimum SCN of archived files in the backup set |
| MAX_NEXT_CHANGE#   | BIGINT  | Maximum SCN of archived files in the backup set |
| RESTORE_TIME   | DATE | The last restore time of the database when this backup set was created; displays as empty if the DB has not executed a RESTORE operation. |
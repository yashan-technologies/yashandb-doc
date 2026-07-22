This view displays information about all archived log file backup sets in the system.

|Field |Type |Description |
| --- | --- | --- |
| RECID# | BIGINT  | Backup set number |
| INSTANCE\_NUMBER# | INTEGER      | Instance number |
| START\_TIME | DATE | Start time, derived from the internal SCN, may have discrepancies with actual machine time |
| COMPLETION\_TIME | DATE | Completion time, derived from the internal SCN, may have discrepancies with actual machine time |
| TYPE  | VARCHAR(11) | Backup type<br>* FULL: Full backup <br>* INCREMENTAL: Incremental backup<br>* ARCHIVE: Archive backup |
| PATH  | VARCHAR(256) | Backup set path |
| TAG   | VARCHAR(64) | Unique label |
| COMPRESS\_ALGO  | VARCHAR(4) | Compression algorithm |
| COMPRESS\_LEVEL | VARCHAR(6) | Compression level |
| ENCRYPT\_ALGO   | VARCHAR(6) | Encryption algorithm |
| INPUT\_BYTES    | BIGINT  | Total bytes read during the backup process |
| OUTPUT\_BYTES   | BIGINT  | Total bytes written to disk during the backup process |
| SEQUENCE\_BEGIN# | INTEGER | Minimum ASN of archived files in the backup set |
| SEQUENCE\_END# | INTEGER  | Maximum ASN of archived files in the backup set |
| MIN\_FIRST\_CHANGE#   | BIGINT | Minimum SCN of archived files in the backup set |
| MAX\_NEXT\_CHANGE#   | BIGINT  | Maximum SCN of archived files in the backup set |
| RESTORE\_TIME   | DATE | The last restore time of the database when this backup set was created; displays as empty if the DB has not executed a RESTORE operation. |
| BS\_STATUS| VARCHAR(10) | Current status of the backup set. <br>\* AVAILABLE: The backup set is complete and usable. <br>\* INCOMPLETE: The backup set is partially completed. (In multitenant deployments, this occurs when not all PDBs were successfully backed up) |
| CON\_ID | BIGINT |This field indicates the ID of the container (the CDB root or PDB) to which the backup set belongs. |
| CON\_UID | VARCHAR(64) | This field indicates the UID of the container (the CDB root or PDB) to which the backup set belongs.|
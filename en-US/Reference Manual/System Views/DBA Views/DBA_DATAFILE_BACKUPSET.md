This view displays information about all backup sets in the system.

|Field |Type |Description |
| ---               | ---             | --- |
| RECID#            | BIGINT          | Backup set number |                  
| START\_TIME       | DATE            | Start time, derived from internal SCN; may have an error compared to the actual machine time |                   
| COMPLETION\_TIME  | DATE            | Completion time, derived from internal SCN; may have an error compared to the actual machine time |
| TYPE              | VARCHAR(10)     | Backup type<br>\* FULL: Full backup <br>\* INCREMENTAL: Incremental backup |
| INCREMENT\_LEVEL  | INTEGER         | Level of incremental backup<br>\* 0: All data backed up <br>\* 1: Indicates that only incremental data is backed up based on a certain incremental backup set |
| INCREMENT\_ID#    | INTEGER         | Sequence number within the incremental backup set |
| PATH              | VARCHAR(256)    | Backup set path |
| TAG               | VARCHAR(64)     | Unique tag   |
| IDENT             | BIGINT          | Unique ID of Data file |
| TRUNC\_LSN        | BIGINT          | Baseline log sequence number of the data file; redo logs prior to this sequence do not need to be applied |
| FILE#             | BIGINT          | Data file ID |
| TS#               | BIGINT          | Tablespace ID of the data file |
| TSNAME            | VARCHAR(64)     | Tablespace name of the data file |
| COMPRESS\_ALGO    | VARCHAR(4)      | Compression algorithm |
| COMPRESS\_LEVEL   | VARCHAR(6)      | Compression level |
| ENCRYPT\_ALGO     | VARCHAR(6)      | Encryption algorithm |
| INPUT\_BYTES      | BIGINT          | Total bytes read during the backup process |
| OUTPUT\_BYTES     | BIGINT          | Total bytes written during the backup process |
| RCY\_BEGIN\_ASN    | INTEGER         | ASN corresponding to the start point of applying the backup set: archive sequence number, the smallest ASN among all nodes in a YAC |
| RCY\_BEGIN\_LFN    | BIGINT          | LFN corresponding to the start point of applying the backup set: log sequence number |
| RCY\_END\_ASN      | INTEGER         | ASN corresponding to the end point of applying the backup set: archive sequence number, the largest ASN among all nodes in a YAC |
| RCY\_END\_LFN      | BIGINT          | LFN corresponding to the end point of applying the backup set: log sequence number |
| BS\_KEY           | BIGINT          | Unique identifier for the current backup set |
| IS\_BUCKET        | BOOLEAN         | Indicates whether it is a SLICE file backup set |
| RESTORE\_TIME     | DATE            | The time of the most recent restore operation when the current backup set was created; displays empty if the database has never performed a RESTORE operation |
| CHECKPOINT\_TIME  | TIMESTAMP(6)       | Time point for database consistency recovery; if empty, the database has been upgraded from an old version to a new version, and this item need not be referenced |
| CHECKPOINT\_SCN   | BIGINT          | SCN for database consistency recovery |
| BS\_STATUS| VARCHAR(10) | Current status of the backup set. <br>\* AVAILABLE: The backup set is complete and usable. <br>\* INCOMPLETE: The backup set is partially completed. (In multitenant deployments, this occurs when not all PDBs were successfully backed up) |
| CON\_ID | BIGINT |This field indicates the ID of the container (the CDB root or PDB) to which the backup set belongs. |
| CON\_UID | VARCHAR(64) | This field indicates the UID of the container (the CDB root or PDB) to which the backup set belongs.|
This view displays information about all backup sets in the system.

|Field |Type |Description |
| ---               | ---             | --- |
| RECID#            | BIGINT          | Backup set number |                  
| START_TIME       | DATE            | Start time, derived from internal SCN; may have an error compared to the actual machine time |                   
| COMPLETION_TIME  | DATE            | Completion time, derived from internal SCN; may have an error compared to the actual machine time |
| TYPE              | VARCHAR(10)     | Backup type<br>\* FULL: Full backup <br>\* INCREMENTAL: Incremental backup |
| INCREMENT_LEVEL  | INTEGER         | Level of incremental backup<br>\* 0: All data backed up <br>\* 1: Indicates that only incremental data is backed up based on a certain incremental backup set |
| INCREMENT_ID#    | INTEGER         | Sequence number within the incremental backup set |
| PATH              | VARCHAR(256)    | Backup set path |
| TAG               | VARCHAR(64)     | Unique tag   |
| IDENT             | BIGINT          | Unique ID of Data file |
| TRUNC_LSN        | BIGINT          | Baseline log sequence number of the data file; redo logs prior to this sequence do not need to be applied |
| FILE#             | BIGINT          | Data file ID |
| TS#               | BIGINT          | Tablespace ID of the data file |
| TSNAME            | VARCHAR(64)     | Tablespace name of the data file |
| COMPRESS_ALGO    | VARCHAR(4)      | Compression algorithm |
| COMPRESS_LEVEL   | VARCHAR(6)      | Compression level |
| ENCRYPT_ALGO     | VARCHAR(6)      | Encryption algorithm |
| INPUT_BYTES      | BIGINT          | Total bytes read during the backup process |
| OUTPUT_BYTES     | BIGINT          | Total bytes written during the backup process |
| RCY_BEGIN_ASN    | INTEGER         | ASN corresponding to the start point of applying the backup set: archive sequence number, the smallest ASN among all nodes in a YAC |
| RCY_BEGIN_LFN    | BIGINT          | LFN corresponding to the start point of applying the backup set: log sequence number |
| RCY_END_ASN      | INTEGER         | ASN corresponding to the end point of applying the backup set: archive sequence number, the largest ASN among all nodes in a YAC |
| RCY_END_LFN      | BIGINT          | LFN corresponding to the end point of applying the backup set: log sequence number |
| BS_KEY           | BIGINT          | Unique identifier for the current backup set |
| IS_BUCKET        | BOOLEAN         | Indicates whether it is a SLICE file backup set |
| RESTORE_TIME     | DATE            | The time of the most recent restore operation when the current backup set was created; displays empty if the database has never performed a RESTORE operation |
| CHECKPOINT_TIME  | TIMESTAMP       | Time point for database consistency recovery; if empty, the database has been upgraded from an old version to a new version, and this item need not be referenced |
| CHECKPOINT_SCN   | BIGINT          | SCN for database consistency recovery |
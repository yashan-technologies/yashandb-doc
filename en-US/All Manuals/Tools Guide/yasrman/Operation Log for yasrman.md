*yasrman* tool has its own independent runtime log, which records the execution process and detailed information of the *yasrman* commands.

## Log Storage Path

The runtime log is saved by default in the catalog directory, named run.log. The run.log file is created simultaneously when the catalog is created using the *yasrman* create catalog command.

## Log Level

The log level used by the *yasrman* tool is INFO, and it cannot be modified externally.

***Example***

```verilog
// yasrman create catalog
2023-07-11 11:03:37.038 32120 [INFO] [YASRMAN] create catalog success, path: /data/yashan/catalog

// yasrman executes backup
2023-07-11 11:04:11.930 32239 [INFO] [YASRMAN] start backup database, type 2, level 0, path , parallelism 2, section size 134217728, is distribution: FALSE, is rmanside: TRUE
2023-07-11 11:04:12.253 32245 [INFO] [YASRMAN BACKUP]: starting backing up file: /data/yashan/catalog/backup/bak1/ctrl_0_0_0.bak 
2023-07-11 11:04:12.305 32245 [INFO] [YASRMAN BACKUP]: end backing up file: /data/yashan/catalog/backup/bak1/ctrl_0_0_0.bak
2023-07-11 11:04:12.316 32245 [INFO] [YASRMAN BACKUP]: starting backing up file: /data/yashan/catalog/backup/bak1/data_0_0_0.bak 
2023-07-11 11:04:12.318 32247 [INFO] [YASRMAN BACKUP]: starting backing up file: /data/yashan/catalog/backup/bak1/data_1_0_0.bak 
2023-07-11 11:04:12.457 32245 [INFO] [YASRMAN BACKUP]: end backing up file: /data/yashan/catalog/backup/bak1/data_0_0_0.bak 
2023-07-11 11:04:12.467 32245 [INFO] [YASRMAN BACKUP]: starting backing up file: /data/yashan/catalog/backup/bak1/data_2_0_0.bak 
2023-07-11 11:04:12.468 32245 [INFO] [YASRMAN BACKUP]: end backing up file: /data/yashan/catalog/backup/bak1/data_2_0_0.bak
2023-07-11 11:04:12.474 32247 [INFO] [YASRMAN BACKUP]: end backing up file: /data/yashan/catalog/backup/bak1/data_1_0_0.bak
2023-07-11 11:04:12.478 32245 [INFO] [YASRMAN BACKUP]: starting backing up file: /data/yashan/catalog/backup/bak1/data_3_0_0.bak
2023-07-11 11:04:12.479 32245 [INFO] [YASRMAN BACKUP]: end backing up file: /data/yashan/catalog/backup/bak1/data_3_0_0.bak 
2023-07-11 11:04:12.485 32247 [INFO] [YASRMAN BACKUP]: starting backing up file: /data/yashan/catalog/backup/bak1/data_4_0_0.bak 
2023-07-11 11:04:12.489 32245 [INFO] [YASRMAN BACKUP]: starting backing up file: /data/yashan/catalog/backup/bak1/data_5_0_0.bak 
2023-07-11 11:04:12.627 32247 [INFO] [YASRMAN BACKUP]: end backing up file: /data/yashan/catalog/backup/bak1/data_4_0_0.bak 
2023-07-11 11:04:12.790 32245 [INFO] [YASRMAN BACKUP]: end backing up file: /data/yashan/catalog/backup/bak1/data_5_0_0.bak 
2023-07-11 11:04:12.801 32245 [INFO] [YASRMAN BACKUP]: starting backing up file: /data/yashan/catalog/backup/bak1/arch0_0_2_0.bak 
2023-07-11 11:04:12.802 32245 [INFO] [YASRMAN BACKUP]: end backing up file: /data/yashan/catalog/backup/bak1/arch0_0_2_0.bak
2023-07-11 11:04:12.814 32245 [INFO] [YASRMAN BACKUP]: starting backing up file: /data/yashan/catalog/backup/bak1/backup_profile 
2023-07-11 11:04:12.863 32245 [INFO] [YASRMAN BACKUP]: end backing up file: /data/yashan/catalog/backup/bak1/backup_profile 
2023-07-11 11:04:12.870 32245 [INFO] [YASRMAN BACKUP]: starting backing up file: /data/yashan/catalog/backup/bak1/backup_filelist
2023-07-11 11:04:12.870 32245 [INFO] [YASRMAN BACKUP]: end backing up file: /data/yashan/catalog/backup/bak1/backup_filelist
2023-07-11 11:04:13.691 32239 [INFO] backup successfully

// yasrman deletes the backup set as follows
2023-07-11 11:08:01.881 32662 [INFO] [YASRMAN DELETE] start drop backupset, tag bak1
2023-07-11 11:08:01.950 32662 [INFO] delete backupset successfully
```

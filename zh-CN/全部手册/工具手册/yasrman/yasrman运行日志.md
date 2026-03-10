yasrman工具有自身独立的运行日志，记录yasrman命令执行过程和详细信息。

## 日志存放路径

运行日志默认保存在catalog目录下，以run.log命名。使用yasrman的create catalog命令创建catalog的同时创建run.log文件。

## 日志级别

yasrman工具使用的日志级别为INFO，且外部无法修改。

示例

```verilog
// yasrman创建catalog
2023-07-11 11:03:37.038 32120 [INFO] [YASRMAN] create catalog success, path: /data/yashan/catalog

// yasrman执行备份时
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

// yasrman删除备份集显示如下 
2023-07-11 11:08:01.881 32662 [INFO] [YASRMAN DELETE] start drop backupset, tag bak1
2023-07-11 11:08:01.950 32662 [INFO] delete backupset successfully
```

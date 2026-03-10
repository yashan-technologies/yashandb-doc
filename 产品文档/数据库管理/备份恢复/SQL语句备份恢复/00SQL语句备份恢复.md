## 数据库备份恢复

通过SQL语句执行的备份与恢复操作适用于单机部署或共享集群部署的数据库。

**操作示例**

以下为对单机部署的数据库执行备份恢复的模拟场景：

1. 检查并开启数据库的归档模式。

      ```shell
      -- 查看当前数据库的归档模式
      SELECT database_name,log_mode,open_mode FROM V$DATABASE;
 
      DATABASE_NAME      LOG_MODE          OPEN_MODE  
      ------------------ ----------------- ------------
      yashandb              ARCHIVELOG        READ_WRITE  
      ```

      LOG_MODE为ARCHIVELOG表示归档模式，直接执行后续操作即可。若LOG_MODE为NOARCHIVELOG表示非归档模式，需先将数据库[切换为归档模式](../../基本数据库管理/归档管理)。

2. 模拟业务场景，在数据库中创建表并插入数据。

      ```sql
      CREATE TABLE backuptable (b1 INT,b2 INT); 
      
      INSERT INTO backuptable VALUES (2,3);
      
      COMMIT;
      ```

3. 执行数据全量备份。

      ```sql
      BACKUP DATABASE FULL FORMAT '/YashanDB/backup/full_20211212181530';
      ```

      > **Caution**：
      >
      > 确保“/YashanDB/backup”目录存在且YashanDB安装用户具有读写权限，如果指定的数据备份目录已存在，则会触发YAS-00318错误。

4. 使用具有DBA权限的用户查询DBA_BACKUP_SET视图，检查备份详情。

      ```sql
      SELECT RECID#,START_TIME,COMPLETION_TIME,TYPE,INCREMENT_LEVEL,INCREMENT_ID#,PATH,TAG,TRUNC_LSN,COMPRESS_LEVEL,INPUT_BYTES,OUTPUT_BYTES,RCY_BEGIN_ASN,RCY_BEGIN_LFN,RCY_END_ASN,RCY_END_LFN,COMPRESS_ALGO,ENCRYPT_ALGO FROM DBA_BACKUP_SET;

      
            RECID# START_TIME                       COMPLETION_TIME                  TYPE          INCREMENT_LEVEL INCREMENT_ID# PATH                                                             TAG                                                                          TRUNC_LSN COMPRESS_LEVEL           INPUT_BYTES          OUTPUT_BYTES RCY_BEGIN_ASN         RCY_BEGIN_LFN  RCY_END_ASN           RCY_END_LFN COMPRESS_ALGO ENCRYPT_ALGO 
      ------------ -------------------------------- -------------------------------- ------------- --------------- ------------- ---------------------------------------------------------------- ---------------------------------------------------------------- --------------------- -------------- --------------------- --------------------- ------------- --------------------- ------------ --------------------- ------------- ------------ 
            1 2023-06-19                       2023-06-19                       FULL                        0             0 /YashanDB/backup/full_20211212181530                      bak_2023061902151684                                                             53767 NONE                       516325376             516325376             8                 51469            8                 51471 NONE          NONE        
                        
      ```

5. 检查生成的备份集物理文件。

      ```sql
      $ cd /YashanDB/backup/full_20211212181530 
      $ ls -rlt
      total 504232
      -rw-r-----. 1 yashan yashan  28729344 Jun 19 02:15 ctrl_0_0_0.bak
      -rw-r-----. 1 yashan yashan  67108864 Jun 19 02:15 data_0_0_0.bak
      -rw-r-----. 1 yashan yashan  67108864 Jun 19 02:15 data_1_0_0.bak
      -rw-r-----. 1 yashan yashan      8192 Jun 19 02:15 data_3_0_0.bak
      -rw-r-----. 1 yashan yashan      8192 Jun 19 02:15 data_2_0_0.bak
      -rw-r-----. 1 yashan yashan  67108864 Jun 19 02:15 data_4_0_0.bak
      -rw-r-----. 1 yashan yashan 134217728 Jun 19 02:15 data_5_0_0.bak
      -rw-r-----. 1 yashan yashan 134217728 Jun 19 02:15 data_5_0_1.bak
      -rw-r-----. 1 yashan yashan   1040384 Jun 19 02:15 arch_0_8_0.bak
      drwx------. 2 yashan yashan         6 Jun 19 02:15 bucket_4_0_0.bak
      -rw-r-----. 1 yashan yashan  16777216 Jun 19 02:15 backup_profile
      -rw-r-----. 1 yashan yashan      5120 Jun 19 02:15 backup_filelist
      ```

      - ctrl_*文件为备份的控制文件。

      - data_*文件为备份的数据文件。

      - arch_*文件为备份的归档文件。

      - redo_*文件为备份的在线日志文件，仅在备库执行备份生成的备份集中存在。

      - 当业务中存在LSC表时，bucket_4_0_0.bak/下存放LSC表的可变数据文件。

      - backup_profile文件为备份集元数据文件，用于备份集和数据库版本的校验，备份集恢复关键信息等。

      - backup_filelist文件用于校验备份集数据的完整性正确性。

6. 模拟灾难场景，删除数据库文件。

      > **Warn**：
      >
      > 以下操作只能在测试环境上执行，请勿直接在生产环境上测试。

      ```shell
      # $YASDB_DATA/dbfiles为数据库文件路径
      $ cd $YASDB_DATA/dbfiles
      $ rm -rf ./*
      $ ls -rlt
      total 0
      ```

7. 重启数据库到NOMOUNT状态。

      ```shell
      SQL> shutdown immediate;
      SQL> exit;
      
      $ yasboot cluster start -c yashandb -m nomount
      $ yasql sys/********
      ```

8. 执行恢复，然后检查新增数据是否恢复。
      
      ```sql
      RESTORE DATABASE FROM '/YashanDB/backup/full_20211212181530';
      RECOVER DATABASE;
      ALTER DATABASE OPEN;
      SELECT b1,b2 FROM backuptable;

            B1           B2 
      ------------ ------------ 
            2            3
      ```

## 归档文件备份恢复

通过SQL语句执行的归档日志备份与恢复操作适用于单机部署或共享集群部署的数据库。

本文以单机部署为例模拟执行归档备份和恢复操作。

### 备份示例

使用本方式执行备份时，要求数据库运行于OPEN状态且归档模式开启。

1. 检查并开启数据库的归档模式。

      ```shell
      -- 查看当前数据库的归档模式
      SELECT database_name,log_mode,open_mode FROM V$DATABASE;
 
      DATABASE_NAME      LOG_MODE          OPEN_MODE  
      ------------------ ----------------- ------------
      yashandb              ARCHIVELOG        READ_WRITE  
      ```

      LOG_MODE为ARCHIVELOG表示归档模式，直接执行后续操作即可。若LOG_MODE为NOARCHIVELOG表示非归档模式，需先将数据库[切换为归档模式](../../基本数据库管理/归档管理)。

2. 模拟业务场景，在数据库中创建表并插入数据，并执行多次切换LOGFILE生成多个归档。

      ```sql
      CREATE TABLE backuptable (b1 INT,b2 INT); 
      
      INSERT INTO backuptable VALUES (2,3);
      
      COMMIT;

      -- 执行多次
      ALTER SYSTEM SWITCH LOGFILE;

      -- 查看已生成归档
      SELECT SEQUENCE#,FIRST_CHANGE#,NEXT_CHANGE# FROM V$ARCHIVED_LOG;
      ```

3. 指定归档的SEQUENCE区间备份。

      ```sql
      SELECT SEQUENCE#,FIRST_CHANGE#,NEXT_CHANGE# FROM V$ARCHIVED_LOG;

      SEQUENCE#         FIRST_CHANGE#          NEXT_CHANGE# 
      ------------ --------------------- --------------------- 
            1    525940019749924864    525940036244176896
            2    525940036244176896    525940261064138752
            3    525940261064138752    525940261064138752
            4    525940261064138752    525940261064138752
            5    525940261064138752    525940400603951104


      -- 执行备份
      BACKUP ARCHIVELOG SEQUENCE BETWEEN  2 AND 5  FORMAT '/YashanDB/backup/SEQUENCE_2_5';

      ```

      > **Note**：
      >
      > 上述示例中在生成归档日志期间未做任何业务操作，所以部分归档的SCN起始是一样的，实际生产环境则可能表现不同。


4. 使用具有DBA权限的用户查询DBA_ARCHIVE_BACKUPSET视图，检查备份详情。

      ```sql
      SELECT RECID#,INSTANCE_NUMBER#,START_TIME,COMPLETION_TIME,TYPE,PATH,TAG,COMPRESS_ALGO,COMPRESS_LEVEL,ENCRYPT_ALGO,INPUT_BYTES,OUTPUT_BYTES,SEQUENCE_BEGIN#,SEQUENCE_END#,MIN_FIRST_CHANGE#,MAX_NEXT_CHANGE#,RESTORE_TIME FROM DBA_ARCHIVE_BACKUPSET;

                  RECID# INSTANCE_NUMBER# START_TIME                                       COMPLETION_TIME                                  TYPE          PATH                                                             TAG                                                              COMPRESS_ALGO COMPRESS_LEVEL ENCRYPT_ALGO           INPUT_BYTES          OUTPUT_BYTES SEQUENCE_BEGIN# SEQUENCE_END#     MIN_FIRST_CHANGE#      MAX_NEXT_CHANGE# RESTORE_TIME                                     
      --------------------- ---------------- ------------------------------------------------ ------------------------------------------------ ------------- ---------------------------------------------------------------- ---------------------------------------------------------------- ------------- -------------- ------------ --------------------- --------------------- --------------- ------------- --------------------- --------------------- ------------------------------------------------ 
                        1                1 2024-01-26                                       2024-01-26                                       ARCHIVE       /YashanDB/backup/SEQUENCE_2_5                                       bak_2024012611380123                                             NONE          NONE           NONE                      17362944              17362944               2             5    525940036244176896    525940400603951104                                                 

      ```

5. 检查生成的备份集物理文件。

      ```sql
      $ cd /YashanDB/backup/SEQUENCE_2_5 
      $ ls -rlt
      total 16960
      -rw-r----- 1 zhangxt zhangxt    12288 Jan 26 11:38 arch0_0_3_0.bak
      -rw-r----- 1 zhangxt zhangxt   544768 Jan 26 11:38 arch0_0_2_0.bak
      -rw-r----- 1 zhangxt zhangxt    12288 Jan 26 11:38 arch0_0_4_0.bak
      -rw-r----- 1 zhangxt zhangxt    16384 Jan 26 11:38 arch0_0_5_0.bak
      -rw-r----- 1 zhangxt zhangxt 16777216 Jan 26 11:38 backup_profile
      -rw-r----- 1 zhangxt zhangxt     2560 Jan 26 11:38 backup_filelist
      ```

      - arch*为独立的归档文件，arch{instanceId}_{resetId}_{asn}_{secId}，分别表示节点的ID，resetlogs的id，归档文件的序列号ASN，归档文件的分片序列号。

      - backup_profile文件为备份集元数据文件，用于备份集和数据库版本的校验，备份集恢复关键信息等。

      - backup_filelist文件用于校验备份集数据的完整性正确性。

### 恢复示例

使用本方式执行恢复时，要求当前数据库实例处于执行完RESTORE且未执行RECOVER的MOUNT状态，然后检查归档文件是否恢复到指定目录。

除指定SEQUENCE区间外，也可指定为时间、SCN区间，详情请查阅[BACKUP ARCHIVELOG](../../../开发手册/SQL参考手册/SQL语句（yashan模式）/BACKUP ARCHIVELOG)。

```sql
$ cd /YASDATA/archive 
$ ll -rlt
total 23296
-rw-r----- 1 yashan yashan    36864 Nov 29 09:41 arch_0_1.ARC
-rw-r----- 1 yashan yashan 23781376 Nov 29 09:46 arch_0_2.ARC
-rw-r----- 1 yashan yashan    12288 Nov 29 09:47 arch_0_3.ARC
-rw-r----- 1 yashan yashan    12288 Nov 29 09:47 arch_0_4.ARC
-rw-r----- 1 yashan yashan    12288 Nov 29 09:47 arch_0_5.ARC

-- 模拟归档文件丢失，请勿在生产环境上测试
$ rm arch_0_3.ARC arch_0_5.ARC

$ ll -rlt
total 23272
-rw-r----- 1 yashan yashan    36864 Nov 29 09:41 arch_0_1.ARC
-rw-r----- 1 yashan yashan 23781376 Nov 29 09:46 arch_0_2.ARC
-rw-r----- 1 yashan yashan    12288 Nov 29 09:47 arch_0_4.ARC

-- 恢复指定SEQUENCE的范围为2-4，即只恢复该区间内的归档
SQL> RESTORE ARCHIVELOG SEQUENCE BETWEEN 2 AND 4 FROM SEARCHDIR '/YashanDB/backup';


-- 检查文件仅恢复了SEQUENCE为3的归档文件。
$ ll -rlt
total 23284
-rw-r----- 1 yashan yashan    36864 Nov 29 09:41 arch_0_1.ARC
-rw-r----- 1 yashan yashan 23781376 Nov 29 09:46 arch_0_2.ARC
-rw-r----- 1 yashan yashan    12288 Nov 29 09:47 arch_0_4.ARC
-rw-r----- 1 yashan yashan    12288 Nov 29 10:17 arch_0_3.ARC
```

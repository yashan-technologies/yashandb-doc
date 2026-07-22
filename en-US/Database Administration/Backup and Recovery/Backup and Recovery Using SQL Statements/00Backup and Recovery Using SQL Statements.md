## Database Backup and Recovery

Backup and recovery operations executed via SQL statements are suitable for databases in Standalone/YAC/Distributed Cluster Deployment.

**Operation Example**

The following is a simulated scenario for performing backup and recovery on a Standalone Deployment database:

1. Check and enable the database's archiving mode.

      ```shell
      -- Check the current database's archiving mode 
      SELECT database_name,log_mode,open_mode FROM V$DATABASE;
 
      DATABASE_NAME      LOG_MODE          OPEN_MODE  
      ------------------ ----------------- ------------
      yashandb              ARCHIVELOG        READ_WRITE  
      ```

      LOG_MODE being ARCHIVELOG indicates archiving mode, and you can directly execute the subsequent operations. If LOG_MODE is NOARCHIVELOG, it indicates non-archiving mode, and you need to first switch the database to [archiving mode](../../Instance Management/Archive Management).

2. Simulate a business scenario, create a table in the database, and insert data.

      ```sql
      CREATE TABLE backuptable (b1 int,b2 int); 
      
      INSERT INTO backuptable VALUES (2,3);
      
      commit;
      ```

3. Perform a full data backup.

      ```sql
      backup database full format '/YashanDB/backup/full_20211212181530';
      ```

      > **Caution**: 
      >
      > Ensure the directory "/YashanDB/backup" exists and that the YashanDB installation user has read and write privileges. If the specified data backup directory already exists, it will trigger the YAS-00318 error.

4. Use a user with DBA privileges to query the DBA_BACKUP_SET view and check the backup details.

      ```sql
      SELECT RECID#,START_TIME,COMPLETION_TIME,TYPE,INCREMENT_LEVEL,INCREMENT_ID#,PATH,TAG,TRUNC_LSN,COMPRESS_LEVEL,INPUT_BYTES,OUTPUT_BYTES,RCY_BEGIN_ASN,RCY_BEGIN_LFN,RCY_END_ASN,RCY_END_LFN,COMPRESS_ALGO,ENCRYPT_ALGO FROM DBA_BACKUP_SET;

            RECID# START_TIME                       COMPLETION_TIME                  TYPE          INCREMENT_LEVEL INCREMENT_ID# PATH                                                             TAG                                                                          TRUNC_LSN COMPRESS_LEVEL           INPUT_BYTES          OUTPUT_BYTES RCY_BEGIN_ASN         RCY_BEGIN_LFN  RCY_END_ASN           RCY_END_LFN COMPRESS_ALGO ENCRYPT_ALGO 
      ------------ -------------------------------- -------------------------------- ------------- --------------- ------------- ---------------------------------------------------------------- ---------------------------------------------------------------- --------------------- -------------- --------------------- --------------------- ------------- --------------------- ------------ --------------------- ------------- ------------ 
            1 2023-06-19                       2023-06-19                       FULL                        0             0 /YashanDB/backup/full_20211212181530                      bak_2023061902151684                                                             53767 NONE                       516325376             516325376             8                 51469            8                 51471 NONE          NONE        
                        
      ```

5. Check the generated backup set physical files.

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

      - ctrl_* files are backup control files.

      - data_* files are backup data files.

      - arch_* files are backup archive files.

      - redo_* files are backup online log files, which only exist in backup sets generated in standby databases.

      - When there are LSC tables in the business, variable data files of the LSC table are stored under bucket_4_0_0.bak/.

      - backup_profile file is the backup set meta data file, used for verification of backup sets and database versions, recovery key information of backup sets, etc.

      - backup_filelist file is used to verify the integrity and correctness of backup set data.

6. Simulate a disaster scenario by deleting database files.

      > **Warn**:
      >
      > The following operations should only be performed in a testing environment, do not test directly in a production environment.

      ```shell
      # $YASDB_DATA/dbfiles is the path for database files.
      $ cd $YASDB_DATA/dbfiles
      $ rm -rf ./*
      $ ls -rlt
      total 0
      ```

7. Restart the database to NOMOUNT status.

      ```shell
      SQL> shutdown immediate;
      SQL> exit;
      
      $ yasboot cluster start -c yashandb -m nomount
      $ yasql sys/********
      ```

8. Execute recovery and then check if the newly added data is restored.
      
      ```sql
      restore database from '/YashanDB/backup/full_20211212181530';
      recover database;
      alter database open;
      select b1,b2 from backuptable;

            B1           B2 
      ------------ ------------ 
            2            3
      ```

## Archive File Backup and Recovery

Backup and recovery operations executed via SQL statements for archive log files are suitable for databases in Standalone/YAC/Distributed Cluster Deployment.

This article simulates executing archive backup and recovery operations using Standalone Deployment as an example.

### Backup Example

When performing backups using this method, the database must be running in OPEN status with archiving mode enabled.

1. Check and enable the database's archiving mode.

      ```shell
      -- Check the current database's archiving mode 
      SELECT database_name,log_mode,open_mode FROM V$DATABASE;
 
      DATABASE_NAME      LOG_MODE          OPEN_MODE  
      ------------------ ----------------- ------------
      yashandb              ARCHIVELOG        READ_WRITE  
      ```

      LOG_MODE being ARCHIVELOG indicates archiving mode, and you can directly execute the subsequent operations. If LOG_MODE is NOARCHIVELOG, it indicates non-archiving mode, and you need to first switch the database to [archiving mode](../../Instance Management/Archive Management).

2. Simulate a business scenario, create a table in the database, insert data, and perform multiple LOGFILE switches to generate multiple archives.

      ```sql
      CREATE TABLE backuptable (b1 int,b2 int); 
      
      INSERT INTO backuptable VALUES (2,3);
      
      commit;

      -- Execute multiple times
      ALTER SYSTEM SWITCH LOGFILE;

      -- Check the generated archives
      select SEQUENCE#,FIRST_CHANGE#,NEXT_CHANGE# from v$ARCHIVED_LOG;
      ```

3. Specify the SEQUENCE range of the archives to backup.

      ```sql
      select SEQUENCE#,FIRST_CHANGE#,NEXT_CHANGE# from v$archived_log;

      SEQUENCE#         FIRST_CHANGE#          NEXT_CHANGE# 
      ------------ --------------------- --------------------- 
            1    525940019749924864    525940036244176896
            2    525940036244176896    525940261064138752
            3    525940261064138752    525940261064138752
            4    525940261064138752    525940261064138752
            5    525940261064138752    525940400603951104

      5 rows fetched.

      -- Execute backup
      BACKUP ARCHIVELOG SEQUENCE BETWEEN  2 AND 5  FORMAT '/YashanDB/backup/SEQUENCE_2_5';

      Succeed.
      ```

      > **Note**: 
      >
      > In the above example, no business operations were performed during the generation of archive log files, so some archives' SCN starts may be the same. In a real production environment, this may behave differently.

4. Use a user with DBA privileges to query the DBA_ARCHIVE_BACKUPSET view and check the backup details.

      ```sql
      select RECID#,INSTANCE_NUMBER#,START_TIME,COMPLETION_TIME,TYPE,PATH,TAG,COMPRESS_ALGO,COMPRESS_LEVEL,ENCRYPT_ALGO,INPUT_BYTES,OUTPUT_BYTES,SEQUENCE_BEGIN#,SEQUENCE_END#,MIN_FIRST_CHANGE#,MAX_NEXT_CHANGE#,RESTORE_TIME from DBA_ARCHIVE_BACKUPSET;

                  RECID# INSTANCE_NUMBER# START_TIME                                       COMPLETION_TIME                                  TYPE          PATH                                                             TAG                                                              COMPRESS_ALGO COMPRESS_LEVEL ENCRYPT_ALGO           INPUT_BYTES          OUTPUT_BYTES SEQUENCE_BEGIN# SEQUENCE_END#     MIN_FIRST_CHANGE#      MAX_NEXT_CHANGE# RESTORE_TIME                                     
      --------------------- ---------------- ------------------------------------------------ ------------------------------------------------ ------------- ---------------------------------------------------------------- ---------------------------------------------------------------- ------------- -------------- ------------ --------------------- --------------------- --------------- ------------- --------------------- --------------------- ------------------------------------------------ 
                        1                1 2024-01-26                                       2024-01-26                                       ARCHIVE       /YashanDB/backup/SEQUENCE_2_5                                       bak_2024012611380123                                             NONE          NONE           NONE                      17362944              17362944               2             5    525940036244176896    525940400603951104                                                 

      1 row fetched.
      ```

5. Check the generated backup set physical files.

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

      - arch* are standalone archive files, arch{instanceId}_{resetId}_{asn}_{secId}, representing node ID, resetlogs ID, archive file sequence number asn, and archive file chunk sequence number.

      - backup_profile file is the backup set meta data file, used for verification of backup sets and database versions, recovery key information of backup sets, etc.

      - backup_filelist file is used to verify the integrity and correctness of backup set data.

### Recovery Example

When executing recovery using this method, the current database instance must be in MOUNT status after executing RESTORE and before executing RECOVER, and then check whether the archive files have been restored to the specified directory.

Besides specifying a SEQUENCE range, it is also possible to specify time or SCN ranges. For details, please refer to [BACKUP ARCHIVELOG](../../../Development Guide/SQL Reference Manual/SQL Statements/BACKUP ARCHIVELOG).

```sql
$ cd /YASDATA/archive 
$ ll -rlt
total 23296
-rw-r----- 1 yashan yashan    36864 Nov 29 09:41 arch_0_1.ARC
-rw-r----- 1 yashan yashan 23781376 Nov 29 09:46 arch_0_2.ARC
-rw-r----- 1 yashan yashan    12288 Nov 29 09:47 arch_0_3.ARC
-rw-r----- 1 yashan yashan    12288 Nov 29 09:47 arch_0_4.ARC
-rw-r----- 1 yashan yashan    12288 Nov 29 09:47 arch_0_5.ARC

-- Simulate archiving file loss, do not test in a production environment
$ rm arch_0_3.ARC arch_0_5.ARC

$ ll -rlt
total 23272
-rw-r----- 1 yashan yashan    36864 Nov 29 09:41 arch_0_1.ARC
-rw-r----- 1 yashan yashan 23781376 Nov 29 09:46 arch_0_2.ARC
-rw-r----- 1 yashan yashan    12288 Nov 29 09:47 arch_0_4.ARC

-- Recover the specified SEQUENCE range from 2-4, restoring only the archives within that range
SQL> RESTORE ARCHIVELOG SEQUENCE BETWEEN 2 AND 4 FROM SEARCHDIR '/YashanDB/backup';

Succeed.

-- Check that only the archive file with SEQUENCE of 3 has been restored.
$ ll -rlt
total 23284
-rw-r----- 1 yashan yashan    36864 Nov 29 09:41 arch_0_1.ARC
-rw-r----- 1 yashan yashan 23781376 Nov 29 09:46 arch_0_2.ARC
-rw-r----- 1 yashan yashan    12288 Nov 29 09:47 arch_0_4.ARC
-rw-r----- 1 yashan yashan    12288 Nov 29 10:17 arch_0_3.ARC
```

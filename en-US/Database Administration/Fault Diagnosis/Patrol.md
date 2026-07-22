In YashanDB, the inspection operates as a separate background thread, similar to a patrol team, continuously monitoring the operational status of the database. When a severe error occurs, diagnostic data is collected and stored in the automatic diagnostic repository, triggering corresponding remediation measures to limit damage and interruptions.

The inspection mainly includes the following components:

- Monitoring database files
- Triggering health checks upon severe errors
- Monitoring synchronous standby database (maximize protection mode)

## File Monitoring

The background files of YashanDB store critical information, and the loss of certain files may render the database unusable. Additionally, users are not allowed to manually modify database files, as changes to data files can cause various impacts; the system needs to respond promptly to avoid further damage and interruptions. This functionality is disabled in YAC mode.

**Scope of File Monitoring**

Control files, data files, redo files, and if double writing is enabled, double write files will also be monitored.

**Abnormal File Monitoring Operations**

* Changes in file metadata: file privileges, file users and groups, and SCN, etc.
* Files being deleted: files directly deleted, such as using the rm command.
* File names being modified or files being moved: modifications to file names or path changes, such as using the mv command.
* File systems being unmounted: the disk where the file resides is unmounted or damaged and cannot be mounted.

**Abnormal Response**

When anomalies are detected in the database files, an event alert is triggered, event diagnostic data is collected, and a reactive health check is initiated, with diagnostic data stored in the automatic diagnostic repository.

**Fault Handling**

When operations such as data file or redo file deletions or moves occur, the system sets the database status to ABNORMAL, placing the database in read-only mode. Users will encounter errors when executing business operations, waiting for DBA intervention to repair the fault.

Taking the example of a missing data file hm_test, the diagnostic data from the file monitoring is as follows:

```sql
-- Event alert, viewable via V$DIAG_INCIDENT, output as follows

SELECT incident_id, session_id, error_number, error_argument, error_comments FROM v$diag_incident;												  
 INCIDENT_ID   SESSION_ID ERROR_NUMBER ERROR_ARGUMENT    ERROR_COMMENTS                                                   
------------ ------------ ------------ ----------------- ---------------------------------------------------------------- 
          11           12          107 data file         [MONITOR] /data/yashan/dbdata/dbfiles/hm_test does not exist and may have been deleted
          12           12          108 data file         [MONITOR] /data/yashan/dbdata/dbfiles/hm_test was deleted
                   
-- Reactive health check, output information as follows
SELECT run_id, name, check_name, run_mode FROM v$hm_run;				  
 RUN_ID NAME          CHECK_NAME                            RUN_MODE  
------- ------------- ------------------------------------- --------- 
     51 hm_run_51     DB Structure Integrity Check          REACTIVE 

SELECT finding_id, description FROM v$hm_finding WHERE run_id = 51;
  FINDING_ID DESCRIPTION                                                      
------------ ---------------------------------------------------------------- 
          21 datafile /data/yashan/dbdata/dbfiles/hm_test is missing
```

With the loss of the data file, the database status changes to ABNORMAL, and the user cannot execute business operations.

```sql
SELECT status FROM v$database;
STATUS                            
--------------------------------- 
ABNORMAL    

-- Executing business operations results in an error
CREATE TABLE student_no(ID INT);
YAS-06023 database is set to read-only because of database abnormal

-- Normal query
SELECT * FROM DUAL;
DUMMY 
----- 
X    
```

> **Note**: 
> When the database status changes to ABNORMAL, the DBA needs to address it promptly to prevent users from waiting on business operations.
>
> In the case of a missing data file, the database may exit abnormally, requiring prompt DBA response.

For a missing data file, the DBA can consider taking the file offline or performing offline repair on the tablespace containing that file to eliminate the fault status, allowing users to continue business operations. 

An example of using offline operation on the tablespace is as follows:

```sql
-- Eliminate the fault status
ALTER DATABASE CONVERT TO NORMAL;
ALTER DATABASE CONVERT TO NORMAL;
Succeed.

SELECT status FROM v$database;
STATUS                            
--------------------------------- 
NORMAL                           

-- Take the tablespace hm_test offline
ALTER TABLESPACE hm_test OFFLINE IMMEDIATE;
ALTER TABLESPACE hm_test OFFLINE IMMEDIATE;
Succeed.

-- Users can continue executing business operations
CREATE TABLE student_no(ID INT);
CREATE TABLE student_no(ID INT);
Succeed.
```

> **Note**: 
> When ABNORMAL occurs, please promptly check the alert log or the event view V$diag_incident for diagnostic data regarding the database being ABNORMAL.
>
> When ABNORMAL occurs, it is necessary to repair the fault before eliminating it. However, DDL operations on offline tablespaces cannot be executed, so it is necessary to first eliminate the fault status before performing the offline operation.
>
> When using the offline operation, the IMMEDIATE option should be adopted to avoid abnormal exits from the database.
> 
> It is recommended to restart the database to the mount phase before executing offline data file operations, as performing this operation in the open phase presents risks, and the fault may spread.

<span id="AUTO_HM_TEST" name="AUTO_HM_TEST"></span>

## Reactive Health Checks

Reactive health checks are mainly used to investigate potential faults. When YashanDB encounters certain faults during operation, corresponding health checks will be triggered to detect potential faults early and address them in a timely manner to avoid damage.

Taking the example of a failed page read:

```sql
-- Query the data and find that page (block 132) is corrupted
SELECT * FROM HM_TEST;
SELECT * FROM HM_TEST;
YAS-02147 the block 6-0-132 is corrupted

-- Upon discovering page corruption, the inspection thread will trigger a health check (Single Datafile Check) to check the entire file containing this page
-- Trigger a reactive health check, output information as follows
SELECT run_id, name, check_name, run_mode, status FROM v$hm_run;
SELECT run_id, name, check_name, run_mode, status FROM v$hm_run;
RUN_ID NAME        CHECK_NAME              RUN_MODE  STATUS        
------ ----------- ----------------------- --------- -----------
    61 hm_run_61   Single Datafile Check   REACTIVE  COMPLETED    
										 
-- View specific diagnostic data from the health check
SELECT DBMS_HM.GET_RUN_REPORT('HM_RUN_61') FROM DUAL;
SELECT DBMS_HM.GET_RUN_REPORT('HM_RUN_61') FROM DUAL;
DBMS_HM.GET_RUN_REPO                                             
---------------------------------------------------------------- 
 Run Name                    : hm_run_61
 Run Id                      : 61
 Check Name                  : Single Datafile Check
 Mode                        : REACTIVE
 status                      : COMPLETED
 Start Time                  : 2022-07-22 11:51:16
 End Time                    : 2022-07-22 11:51:16
 Error Encountered           : 0
 Source Incident Id          : 0
 Number of Incidents Created : 0

Input Parameters for the Run
 DF_NUM=6

Run Findings And Recommendations
 Finding
 Finding Name  : block corruption
 Finding ID    : 1
 Message       : block 132 in datafile 6: '/data/yashan/dbdata/dbfiles/hm_test' is corrupted
 Message       : object might be unavailable
 Finding
 Finding Name  : block corruption
 Finding ID    : 2
 Message       : block 152 in datafile 6: '/data/yashan/dbdata/dbfiles/hm_test' is corrupted
 Message       : object might be unavailable
```

From the above example, we can see that when business operations reveal page corruption, a health check for the single file is triggered, which not only detects the corruption of page (block 132) but also identifies potential corruption in another page (block 152).

## Monitoring of Synchronous Standby Database (Maximize Protection Mode)

YashanDB's high availability architecture supports three types of [protection modes](../../High Availability/Overview of YashanDB High Availability.md#protection_mode). In maximize protection mode, if the synchronous standby database is disconnected from the primary database or is unavailable, business submissions from primary database users will be stalled.

When the inspection thread detects that the synchronous standby database is unavailable or in a disconnected state for an extended period, it will set the database status to ABNORMAL. Subsequent business operations will not execute, and ongoing or already submitted operations will be stalled, awaiting DBA intervention to repair the fault.

Recommended solutions for handling this fault include:

* Check if the faulty standby database can be restarted or repaired; if so, please do so as soon as possible.
* Consider modifying the relevant configuration parameters QUORUM_SYNC_STANDBYS and REQUIRED_SYNC_STANDBYS for the synchronous standby database as needed.
* Consider adjusting the protection mode of the primary database as needed.

The process for handling this fault is as follows:

1. Executing business results in an error, the database status is ABNORMAL.

      ```sql
      SELECT status FROM v$database;
      STATUS                            
      --------------------------------- 
      ABNORMAL    
      ```

2. Check the V$DIAG_INCIDENT view or alert logs.

      ```sql
      SELECT incident_id, session_id, error_number, error_comments FROM v$diag_incident;

      INCIDENT_ID   SESSION_ID ERROR_NUMBER ERROR_COMMENTS                                                   
      ----------- ------------ ------------ ---------------------------------------------------------------- 
            22           12          211 synchronization standby database destinations have failed, database is set to read-only
      ```

3. View the V$ARCHIVE_DEST_STATUS view.

      ```sql
      -- There is a disconnected standby database
      SELECT dest_id, connection, status, database_mode FROM v$archive_dest_status;
      SELECT dest_id, connection, status, database_mode FROM v$archive_dest_status;
      DEST_ID CONNECTION        STATUS            DATABASE_MODE     
      ------- ----------------- ----------------- ----------------- 
            2 CONNECTED         NORMAL            OPEN             
            3 DISCONNECTED      UNKOWN            UNKOWN       
      ```

4. Select a repair plan (for example, to restart the standby database).

      ```sql
      SELECT dest_id, connection, status, database_mode FROM v$archive_dest_status;
      DEST_ID CONNECTION        STATUS            DATABASE_MODE     
      ------- ----------------- ----------------- ----------------- 
            2 CONNECTED         NORMAL            OPEN             
            3 CONNECTED         NORMAL            OPEN             
      ```

5. The DBA eliminates the fault status, the stalled user business submissions are successfully completed, allowing users to continue with their operations.

      ```sql
      ALTER DATABASE CONVERT TO NORMAL;
      Succeed.

      SELECT status FROM v$database;
      STATUS                            
      --------------------------------- 
      NORMAL    
      ```

      > **Note**: 
      >
      > Typically, if the fault has not been repaired, directly executing ALTER DATABASE CONVERT TO NORMAL to eliminate the fault status will cause the database to detect that the fault still exists and reset the database status to ABNORMAL, recording alert logs and collecting diagnostic data again. However, for faults caused by manual operations on data files, manually eliminating the ABNORMAL status will not reset it back to ABNORMAL, so ensure the fault is repaired before executing the status elimination operation.
      >
      > After some faults are repaired, the database will automatically eliminate the ABNORMAL status, such as:
      >
      > * Insufficient archive space, which sets the database status to ABNORMAL; when the DBA frees up disk space, the database will automatically eliminate the ABNORMAL status, allowing users to continue operations.
      >
	  > * In maximize protection mode, if the synchronous standby database remains abnormal for a long time, the database status is set to ABNORMAL; when the DBA restarts the synchronous standby database or conducts other repairs, the database will automatically eliminate the ABNORMAL status after detecting that the repair is complete, allowing users to continue operations.

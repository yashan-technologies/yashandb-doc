YashanDB provides a health check framework for diagnosing database operations.

Health checks, also known as checkers, inspect various components of the database, such as detecting file corruption, data block corruption, redo log corruption, etc., and generate a report stored in the automatic diagnostic repository's hm directory, documenting the errors found and the impact of those errors.

Health checks can be run in two ways:

* **[Reactive](Patrol.md#AUTO_HM_TEST)**: The fault diagnosis architecture automatically runs health checks in response to severe errors.
* **[Manual](#HM_TEST)**: The built-in advanced package can manually run health checks. If necessary, health checks can also be scheduled periodically by defining a JOB.

Health checks must be run when the database is in the OPEN or MOUNT state.

## Health Check Items

**Database Structural Integrity Check**

This check validates the integrity of database files and reports failures when database files (control files, redo files, data files, archive files) are inaccessible, corrupted, or inconsistent.

**Data Block Integrity Check**

This check can detect corruption in disk image blocks, such as checksum failures, mismatched headers and trailers, and logical inconsistencies within the block. For page corruption, it can usually be repaired by using a standby database or a page repair tool.

**Single Data File Check**

This check can detect the accessibility of a data file, such as complete block corruption of the data file, including valid blocks and unused blocks.

**Redo Integrity Check**

This check can verify the consistency of the database and the header information of all redo files, preventing the database from being unable to recover to a consistent point after a restart.

**Single Redo File Check**

This check can detect the accessibility of a single redo file and redo block corruption.

**Archive Log Files Check**

This check verifies the accessibility and content integrity of a single archive log file.

**Deadlock Detection Check**

This check detects transaction deadlocks due to user actions. When a deadlock is detected, specific deadlock ring information, i.e., Wait-For-Graph (WFG), will be reported in the trace directory (trace files by session id).

<span id="HM_TEST" name="HM_TEST"></span>

## Manually Running Health Checks

Users can manually invoke the DBMS_HM.RUN_CHECK procedure to run health checks, which requires providing the health check name and the run name (customized), as shown below:

```sql
BEGIN
   DBMS_HM.RUN_CHECK('DB Structure Integrity Check', 'my_run', NULL);
END;
/
```

To obtain a list of health check names, run the following query:

```sql
SELECT name FROM v$hm_check;
```

The output will be as follows:

```sql
NAME                                                             
---------------------------------------------------------------- 
DB Structure Integrity Check                                    
Data Block Integrity Check                                      
Single Datafile Check                                           
Redo Integrity Check                                            
Redo File Check                                                 
Archived Log Check
```

Most health checks require input parameters, which can be viewed in the V$HM_CHECK_PARAM view for parameter names and descriptions. Some parameters are mandatory while others are optional. Optional parameters can be set to NULL to use default values. 
The following query displays the parameter information for all health checks:

```sql
SELECT c.name check_name, p.name parameter_name, p.description
FROM v$hm_check_param p, v$hm_check c
WHERE p.check_id = c.id ORDER BY c.name;
```

The output will be as follows:

```sql
CHECK_NAME                       PARAMETER_NAME         DESCRIPTION                 
-------------------------------- ---------------------- --------------------------- 
Archived Log Check               ARC_SEQ_NUM            Archive sequence number    
Data Block Integrity Check       BLC_BL_NUM             Block number               
Data Block Integrity Check       BLC_DF_NUM             Data file number           
Redo File Check                  RF_NUM                 Redo file number           
Single Datafile Check            DF_NUM                 Data file number
```

Input parameter names and values are passed in pairs, separated by semicolons `;`. The following example shows how to pass the file ID and block ID as parameters for the data block integrity check:

```sql
BEGIN
  DBMS_HM.RUN_CHECK('Data Block Integrity Check','my_run','BLC_DF_NUM=1;BLC_BL_NUM=234');
END;
/
```

> **Note**: 
>
> For detailed usage information, please refer to the development guide [DBMS_HM](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_HM) for definitions and parameter descriptions.
>
> The storage limit for health check data is 1,000,000 records. If this limit is exceeded, new diagnostic data will overwrite the oldest diagnostic data.

### Viewing Health Check Reports

The results of health check operations and other information are stored in the automatic diagnostic repository, but reports are not generated immediately.

If users need to view a report, they can execute the DBMS_HM package to generate it. If the report does not exist, the system will first generate the running data from the checker in the automatic diagnostic repository and store it as a TEXT format file in the HM subdirectory under the automatic diagnostic repository's main directory, then output it to the user. If the report file already exists, the system will only perform output display.

The DBMS_HM.GET_RUN_REPORT procedure is used to view health check reports, with the default format being text. An example is as follows:

```sql
SQL> SELECT DBMS_HM.GET_RUN_REPORT('HM_RUN_13') FROM DUAL;

DBMS_HM.GET_RUN_REPO                                             
---------------------------------------------------------------- 
 Run Name                    : hm_run_13
 Run Id                      : 13
 Check Name                  : Data Block Integrity Check
 Mode                        : MANUAL
 status                      : COMPLETED
 Start Time                  : 2022-07-22 10:21:40
 End Time                    : 2022-07-22 10:21:40
 Error Encountered           : 0
 Source Incident Id          : 0
 Number of Incidents Created : 0

Input Parameters for the Run
 BLC_DF_NUM=6
 BLC_BL_NUM=132

Run Findings And Recommendations
 Finding
 Finding Name  : block corruption
 Finding ID    : 12
 Message       : block 132 in datafile 6: '/data/yashan/dbdata/dbfiles/hm_test' is corrupted
 Message       : object might be unavailable
```

> **Note**: 
> For detailed usage information, please refer to the development guide [DBMS_HM](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_HM) for definitions and parameter descriptions.
> If the report information is too long, the system will only output a portion of it; the complete information must be obtained from the report file.

### Health Check Views

Users can directly query the automatic diagnostic repository data that created the report to view the results of specific health checks rather than through health check reports. 

This data can be obtained through the views V$HM_RUN and V$HM_FINDING.

The example below queries the V$HM_RUN view to determine the history of checker runs:

```sql
SQL> SELECT run_id, name, check_name, run_mode FROM v$hm_run;
	 
 RUN_ID NAME              CHECK_NAME                            RUN_MODE  
------- ----------------- ------------------------------------- --------- 
      1 hm_run_1          Single Datafile Check                 REACTIVE 
      2 hm_run_2          Single Datafile Check                 REACTIVE 
      3 hm_run_3          DB Structure Integrity Check          REACTIVE 
      4 hm_run_4          DB Structure Integrity Check          MANUAL   
      .             
      .             
      .             
     39 hm_run_39         Redo Integrity Check                  REACTIVE 
     40 hm_run_40         DB Structure Integrity Check          REACTIVE 
     41 hm_run_41         Data Block Integrity Check            MANUAL         
     42 hm_run_42         Single Datafile Check                 MANUAL   
     43 hm_run_43         DB Structure Integrity Check          REACTIVE 
     44 hm_run_44         Single Datafile Check                 REACTIVE   
     45 hm_run_45         Redo Integrity Check                  MANUAL   
     46 hm_run_46         Redo File Check                       MANUAL   
     47 hm_run_47         Archived Log Check                    MANUAL   
     48 hm_run_48         Redo File Check                       REACTIVE 
```

The following example queries the V$HM_FINDING view to obtain detailed results for run_id=44 (reactive single data file check):

```sql
SQL> SELECT finding_id, description FROM v$hm_finding WHERE run_id = 44;

  FINDING_ID DESCRIPTION                                                      
------------ ---------------------------------------------------------------- 
          16 block 1432 in datafile 6: '/data/yashan/dbdata/dbfiles/hm_test' is corrupted
          17 block 1549 in datafile 6: '/data/yashan/dbdata/dbfiles/hm_test' is corrupted
```

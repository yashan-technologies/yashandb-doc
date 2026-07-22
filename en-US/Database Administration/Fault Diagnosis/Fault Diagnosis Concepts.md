For diagnosing and resolving issues, YashanDB introduces three concepts: Faults, Problems, and Incidents.

## Fault

A fault is a serious error that may occur during the operation of YashanDB, defined internally by the database. Relevant fault information can be viewed through the V$DIAG_FAULT view.

When YashanDB detects an abnormal fault, it prevents the spread of influence by setting the database status to ABNORMAL. The database enters a fault-read-only state, permitting queries but prohibiting write operations.

When the database is in an ABNORMAL state, the v$diag_incident view or alert logs can be checked to clarify the fault's cause.
```sql
-- When a fault occurs, the database status is ABNORMAL
SQL>  SELECT STATUS FROM V$DATABASE;

STATUS                            
--------------------------------- 
ABNORMAL                         

1 row fetched.

-- After the fault is repaired, execute the following statement to eliminate the ABNORMAL state
SQL> ALTER DATABASE CONVERT TO NORMAL;

Succeed.

SQL> SELECT STATUS FROM V$DATABASE;

STATUS                            
--------------------------------- 
NORMAL                           

1 row fetched.
```

The following table lists common faults that set the database status to ABNORMAL along with remedial suggestions.

|Fault Type |Description |Fix Suggestions |
|-------------------------| --- |-------------------------------------------------------------------------------------------------------------------------------------------------|
| Archive log files disk space insufficient | Insufficient archive space can cause operations to stall, setting the status to ABNORMAL to avoid excessive business connections, waiting for DBA to free up space. | After the fault is repaired, manually clear the state or wait for the database to automatically clear it after detecting the fix.<br>\* Increase disk space or free up space.<br>\* Archive cleaning, remove unused archives, refer to [Archive Management](../Instance Management/Archive Management).|
| Maximize protection mode, synchronous standby database anomaly | Under Maximize protection mode, a long-term anomaly in the synchronous standby database can cause the primary database's operations to stall. | After the fault is repaired, manually fix or wait for the database to clear the fault after detecting the repair.<br>\* Investigate the cause, repair synchronous standby.<br>\* Modify the synchronous standby configuration.<br>\* Adjust the protection mode.|
| Data file or redo file manually operated (e.g., rm or mv) | Abnormal operations on data files may render the database unavailable. | First, repair the fault, then manually clear the abnormal state. |
| Fatal errors occur in the database | The database may encounter faults due to resources, IO, etc., causing it to become inoperable. Check the v$diag_incident view or running logs for specific error causes. | This fault state cannot be cleared; the database can no longer continue operating and must undergo a shutdown abort. After repairing the fault, restart the database. |

> **Note**: 
>
> Generally, most faults, when not repaired, will directly clear the ABNORMAL state. If the database detects the fault still exists, it will revert to ABNORMAL state and report events again, such as insufficient archive disk space or anomalies in synchronous standby under maximize protection mode. However, for faults involving manually operated data files, the ABNORMAL state will not revert once manually cleared. Ensure that the fault is repaired before executing the state-clearing operation.

## Problem

A problem is a true fault that occurs during the database's operation; the same fault may record multiple problems, distinguished by problem key values. Problems encountered during database operation can be viewed through the V$DIAG_PROBLEM view.

## Incident

An incident is a single occurrence of a problem. During database operation, different sessions may generate the same problem at different times, leading the database to create multiple incidents. Each incident has a unique ID for distinction and records the current session ID. The V$DIAG_INCIDENT view can be used to view serious errors that occur during the database operation, obtaining more diagnostic data, such as creation time, session ID, fault description, etc.

```sql
-- This view defines all current fault information for YashanDB
SELECT type,code,description from v$DIAG_FAULT;

TYPE              CODE DESCRIPTION                                                      
--------- ------------ ---------------------------------------------------------------- 
YASF               101 cannot allocate memory                                          
YASF               102 too many open files                                             
YASF               103 no space left on device                                         
YASF               104 failed to open file                                             
YASF               105 failed to create file                                           
YASF               106 failed to write file                                            
YASF               107 file metadata changed, for example, permissions, timestamps, extended attributes, and user/group ID, etc
YASF               108 file is missing                                                 
YASF               109 file is moved                                                   
YASF               110 file system is unmounted                                        
YASF               111 failed to read file                                             
YASF               112 failed to extend file                                           
YASF               201 the data file is corrupted                                      
YASF               202 the redo log file is corrupted                                  
YASF               203 the ctrl files are corrupted                                    
YASF               204 data block versions are mismatching                             
YASF               205 the database is aborted                                         
YASF               206 the database needs to be repaired                               
YASF               207 deadlock detected                                               
YASF               208 inconsistent database startup                                   
YASF               209 watcher has an exception                                        
YASF               210 the user limit on the total number of watches was reached       
YASF               211 synchronization standby database destinations have failed    
```

When a problem (serious error) occurs multiple times, the system will create an incident for each occurrence of the problem. These incidents will be stored in the Automatic Diagnostic Repository after being appended with SCN. Each incident is identified by an incident ID, which is unique in the Automatic Diagnostic Repository.

When an incident occurs, the database will perform the following operations:

* Collect diagnostic data from the first failure.
* Mark the incident with an incident ID.
* Dump the incident into a subdirectory created for that incident in the Automatic Diagnostic Repository.

## Incident Flood Control

A problem may generate dozens or hundreds of incidents in a short period, which can lead to excessive diagnostic data being generated in the system, consuming too much space in the Automatic Diagnostic Repository and potentially slowing down the diagnostics and problem resolution process. Due to these reasons, the fault diagnosis mechanism will control the generation of incidents once the flood threshold is reached.

Flood-controlled incidents will only log alerts and will no longer dump incidents. Flood-controlled incidents provide a way to notify users that serious errors are occurring without leading to an overload of diagnostic data.

The threshold levels for incident flood control are predetermined and cannot be changed. They are defined as follows:

* After five incidents of the same problem occur within one hour, subsequent incidents for that problem will be flood-controlled. Normal (non-flood-controlled) logging of incidents for that problem will resume in the next hour.
* After twenty-five incidents of the same problem occur within one day, subsequent incidents for that problem will be flood-controlled. Normal logging of incidents for that problem will resume the following day.
* After fifty incidents of the same problem occur within one hour, or two hundred fifty incidents of the same problem occur within one day, subsequent incidents for that problem will never be recorded in the Automatic Diagnostic Repository. In this case, the database will write a message to the alert log indicating that no additional incidents will be logged.

> **Note**: 
>
> After incident flood control, incidents will no longer be recorded, but the cumulative count of the flooding incidents will be logged.
>
> DBAs should frequently check for new incidents and locate issues promptly.
>
> The storage limit for incident and problem diagnostic data is 1,000,000 entries; if the count exceeds this limit, new diagnostic data will overwrite old diagnostic data.
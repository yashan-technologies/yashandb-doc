The DBMS_AUDIT_MGMT package provides a set of built-in stored procedures for creating and managing audit cleanup tasks. It is important to note that the parameter values for all related procedures must be entered in order.

Only the sys user, users with AUDIT_ADMIN role, and users with DBA role are allowed to call this advanced package.


## SET\_LAST\_ARCHIVE\_TIMESTAMP

```plsql
 DBMS_AUDIT_MGMT.SET_LAST_ARCHIVE_TIMESTAMP(
    audit_trail_type IN INTEGER,
    last_archive_time IN TIMESTAMP);
```

The SET_LAST_ARCHIVE_TIMESTAMP procedure is used to set the cleanup timestamp. The successfully set cleanup timestamp can be queried in the DBA_AUDIT_MGMT_LAST_ARCH_TS view.

|Parameter |Description |
| :------------------------- | :----------------------------------------------------------- |
| audit_trail_type           | Audit cleanup type, currently only supports DBMS_AUDIT_MGMT.AUDIT_TRAIL_UNIFIED. |
| last_archive_time          | Cleanup timestamp, cannot exceed the current time.         |

***Example***

```plsql
-- Set the cleanup timestamp
BEGIN
    DBMS_AUDIT_MGMT.SET_LAST_ARCHIVE_TIMESTAMP(
        DBMS_AUDIT_MGMT.AUDIT_TRAIL_UNIFIED ,
        SYSDATE);
END;
/
```

## CLEAN\_AUDIT\_TRAIL

```plsql
DBMS_AUDIT_MGMT.CLEAN_AUDIT_TRAIL(
    audit_trail_type IN INTEGER,
    use_last_arch_timestamp IN BOOLEAN DEFAULT TRUE);
```

The CLEAN_AUDIT_TRAIL procedure is used to clean up the content of audit logs.
|Parameter |Description |
| :---------------------- | :----------------------------------------------------------- |
| audit_trail_type            | Audit cleanup type, currently only supports DBMS_AUDIT_MGMT.AUDIT_TRAIL_UNIFIED. |
| use_last_arch_timestamp      | Whether to use the cleanup timestamp, value should be TRUE or FALSE. If TRUE, only audit data before the cleanup timestamp is deleted; if FALSE, all audit data is deleted. |

***Example***

```plsql
-- Execute audit log cleanup, clearing audit data before the cleanup timestamp.
BEGIN
  DBMS_AUDIT_MGMT.CLEAN_AUDIT_TRAIL (
        DBMS_AUDIT_MGMT.AUDIT_TRAIL_UNIFIED,
        true);
END;
/
```

## CREATE\_PURGE\_JOB

```plsql
DBMS_AUDIT_MGMT.CREATE_PURGE_JOB(
    audit_trail_type IN INTEGER,
    audit_trail_start_time IN timestamp,
    audit_trail_purge_interval IN varchar,
    audit_trail_purge_name IN VARCHAR,
    use_last_arch_timestamp IN BOOLEAN DEFAULT TRUE);
```

The CREATE_PURGE_JOB procedure is used to create an audit cleanup scheduled job. The successfully created scheduled job can be queried in the DBA_AUDIT_MGMT_CLEANUP_JOBS view, with a default status of enabled when created.

|Parameter |Description |
| :------------------------- | :----------------------------------------------------------- |
| audit_trail_type             | Audit cleanup type, currently only supports DBMS_AUDIT_MGMT.AUDIT_TRAIL_UNIFIED. |
| audit_trail_start_time       | Scheduled job start time.                                   |
| audit_trail_purge_interval    | Expression text to calculate the next execution time of the scheduled job. |
| audit_trail_purge_name       | Scheduled job name.                                        |
| use_last_arch_timestamp       | Whether to use the cleanup timestamp, value should be TRUE or FALSE. If TRUE, only data before the cleanup timestamp is deleted; if FALSE, all audit records are cleaned. |

**Regularly Update the Audit Cleanup Timestamp**

When using scheduled jobs to handle audit data cleanup, by default, audit data prior to the cleanup timestamp will be cleaned. During database usage, audit logs continuously accumulate; therefore, it is necessary to regularly update the cleanup timestamp based on actual needs.

You can use [DBMS_SCHEDULER](DBMS_SCHEDULER).CREATE_JOB provided by YashanDB to regularly update the audit cleanup timestamp. The created job can be queried in DBA_SCHEDULER_JOBS.

***Example***

```plsql
-- Set the audit cleanup timestamp to 30 days ago, keeping only the most recent 30 days of audit logs, refreshed once a day
BEGIN
DBMS_SCHEDULER.CREATE_JOB (
     'update_audit_archive_time',
    'PLSQL_BLOCK',
    'BEGIN DBMS_AUDIT_MGMT.SET_LAST_ARCHIVE_TIMESTAMP(DBMS_AUDIT_MGMT.AUDIT_TRAIL_UNIFIED, sysdate-30);END;' ,
    0,
    SYSDATE,
    'sysdate+1',
    NULL,
    'DEFAULT_JOB_CLASS',
    TRUE,
    FALSE,
    'update audit archive time');
END;
/    
```

**Creating an Audit Cleanup Scheduled Job**

After creating the audit cleanup timestamp scheduled job, create an audit cleanup scheduled job.

***Example***

```plsql
-- Create an automatic cleanup job, starting the first execution after 5 hours with a 1-day interval between executions
BEGIN
  DBMS_AUDIT_MGMT.CREATE_PURGE_JOB (
      DBMS_AUDIT_MGMT.AUDIT_TRAIL_UNIFIED,
      SYSDATE + 5/24,
      'sysdate + 1',
      'audit_job',
     TRUE);
END;
/
```

## SET\_PURGE\_JOB\_STATUS

```plsql
DBMS_AUDIT_MGMT.SET_PURGE_JOB_STATUS(
    audit_trail_purge_name IN VARCHAR,
    audit_trail_status_value IN INTEGER);
```

The SET_PURGE_JOB_STATUS procedure is used to set the enable/disable flag of a scheduled job. A stopped scheduled job will no longer be scheduled by the system.

|Parameter |Description |
| :-------- | :----------------------------------------------------------- |
| audit_trail_purge_name           | Scheduled job name, can be queried in the DBA_AUDIT_MGMT_CLEANUP_JOBS view. |
| audit_trail_status_value         | Whether to stop the scheduled job, DBMS_AUDIT_MGMT.PURGE_JOB_ENABLE indicates enable, DBMS_AUDIT_MGMT.PURGE_JOB_DISABLE indicates disable. |

***Example***

```plsql
-- Stop the audit cleanup scheduled job
BEGIN
  DBMS_AUDIT_MGMT.SET_PURGE_JOB_STATUS (
        'audit_job',
        DBMS_AUDIT_MGMT.PURGE_JOB_DISABLE);
END;
/
```

## SET\_PURGE\_JOB\_INTERVAL

***Example***

```plsql
DBMS_AUDIT_MGMT.SET_PURGE_JOB_INTERVAL(
    audit_trail_purge_name IN VARCHAR,
    audit_trail_interval_value IN VARCHAR);
```

The SET_PURGE_JOB_INTERVAL procedure is used to modify the next execution time of the audit log scheduled job.

|Parameter |Description |
| :--------------------------- | :----------------------------------------------------------- |
| audit_trail_purge_name           | Scheduled job name, can be queried in the DBA_AUDIT_MGMT_CLEANUP_JOBS view. |
| audit_trail_interval_value        | Expression text to calculate the next execution time of the scheduled job. |

***Example***

```plsql
-- Change the execution interval of the audit cleanup scheduled job to 7 days
BEGIN
DBMS_AUDIT_MGMT.SET_PURGE_JOB_INTERVAL(
     'audit_job',
     'sysdate + 7');
END;
/
```

## GET\_LAST\_ARCHIVE\_TIMESTAMP

```plsql
DBMS_AUDIT_MGMT.GET_LAST_ARCHIVE_TIMESTAMP(
    audit_trail_type IN PLS_INTEGER)
RETURN TIMESTAMP;
```

The GET_LAST_ARCHIVE_TIMESTAMP procedure retrieves the audit cleanup timestamp.

|Parameter |Description |
| :-------------------- | :----------------------------------------------------------- |
| audit_trail_type           | Audit cleanup type, currently only supports DBMS_AUDIT_MGMT.AUDIT_TRAIL_UNIFIED. |

***Example***

```plsql
-- Retrieve the audit cleanup timestamp
DECLARE
 last_time TIMESTAMP;
BEGIN
 last_time := DBMS_AUDIT_MGMT.GET_LAST_ARCHIVE_TIMESTAMP(DBMS_AUDIT_MGMT.AUDIT_TRAIL_UNIFIED);
 IF last_time IS NOT NULL THEN
  DBMS_OUTPUT.PUT_LINE('last archive timestamp is: ' || TO_CHAR(last_time));
 ELSE
   DBMS_OUTPUT.PUT_LINE('last archive timestamp is null');
 END IF;
END;
/

--result
last archive timestamp is: 2022-11-05 17:12:34.000000
```

## CLEAR\_LAST\_ARCHIVE\_TIMESTAMP

```plsql
DBMS_AUDIT_MGMT.CLEAR_LAST_ARCHIVE_TIMESTAMP(
    audit_trail_type IN PLS_INTEGER);
```

The CLEAR_LAST_ARCHIVE_TIMESTAMP procedure is used to clear the audit cleanup timestamp.

|Parameter |Description |
| :-------------------- | :----------------------------------------------------------- |
| audit_trail_type           | Audit cleanup type, currently only supports DBMS_AUDIT_MGMT.AUDIT_TRAIL_UNIFIED. |

***Example***

```plsql
-- Clear the audit cleanup timestamp
BEGIN
    DBMS_AUDIT_MGMT.CLEAR_LAST_ARCHIVE_TIMESTAMP
    (DBMS_AUDIT_MGMT.AUDIT_TRAIL_UNIFIED);
END;
/
```

## DROP\_PURGE\_JOB

```plsql
DBMS_AUDIT_MGMT.DROP_PURGE_JOB (
    audit_trail_purge_name IN VARCHAR);
```

The DROP_PURGE_JOB procedure is used to delete a cleanup scheduled job.

|Parameter |Description |
| :------------------------- | :----------------------------------------------------------- |
| audit_trail_purge_name         | Scheduled job name.                                        |

## Exception Explanation

When calling the DBMS_AUDIT_MGMT package for audit cleanup processing, if the parameter settings are incorrect, the following exceptions may be thrown:

|Error Code |Error Content |Explanation |
| :------------- | :------------------------------------------------------------ | :--------------------------------------- |
| YAS-30000       | invalid value of argument AUDIT_TRAIL_TYPE                  | The setting of the audit cleanup type is invalid.   |
| YAS-30001       | invalid value of argument AUDIT_TRAIL_STATUS_VALUE          | The setting of the audit cleanup job status is invalid. |
| YAS-30002       | invalid value of argument LAST_ARCHIVE_TIME                 | The setting of the audit cleanup timestamp is invalid. |
| YAS-30003       | invalid value of argument AUDIT_TRAIL_PURGE_INTERVAL        | The setting of the next execution time is invalid.   |
| YAS-30004       | Cleanup job already existed for the given audit trail type  | The audit cleanup job of the given type already exists. |
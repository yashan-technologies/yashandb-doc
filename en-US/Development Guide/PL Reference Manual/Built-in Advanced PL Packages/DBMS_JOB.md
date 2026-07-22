The DBMS_JOB package provides a set of built-in stored procedures for creating and managing [scheduled jobs](../PL Objects/Scheduled Jobs). It is important to note that all operations performed on scheduled jobs by invoking these stored procedures only take effect after executing COMMIT.

This advanced package is not suitable for ISC Distributed Cluster Deployment.

## SUBMIT

```plsql
DBMS_JOB.SUBMIT(
	job OUT	BIGINT,
    what IN	VARCHAR,
    next_date IN DATE DEFAULT SYSDATE,
    interval IN	VARCHAR DEFAULT NULL,
    no_parse IN	BOOLEAN DEFAULT FALSE,
    instance IN	BINARY_INTEGER DEFAULT 0,
    force IN BOOLEAN DEFAULT FALSE);
```

The SUBMIT procedure is used to create a new scheduled job, and the successfully created scheduled job can be queried in the DBA_JOBS/ALL_JOBS/USER_JOBS views.

|Parameter |Description |
| :-------- |:----------------------------------------------------------------------------|
| job        | The object ID assigned by the system for the scheduled job, of type BIGINT.   |
| what       | The PL/SQL block to be executed by the scheduled job, must end with a semicolon. |
| next_date  | The time for the next execution of the scheduled job.                          |
| interval    | The expression text used to calculate the next execution time of the scheduled job; the calculated time must be in the future or NULL. <br/> If NULL, it indicates the scheduled job will execute only once and be deleted after execution. |
| no_parse   | Indicates whether to parse the PL/SQL text specified by what when creating the scheduled job; true means no parsing, false means parsing and verification will occur. |
| instance   | The instance on which the scheduled job runs; the default value 0 means it can run on any instance. |
| force      | When FALSE, the specified instance must be running; when TRUE, the instance can be any integer. |

Note:

- The instance value can be obtained in YAC/Distributed Cluster Deployment by querying the INSTANCE_NUMBER field of the V$INSTANCE view, while in other deployment modes the instance value is always 1.
- In Standalone Deployment, entering a non-1 instance value will successfully create the scheduled job definition when force=true, but the job will not be executed.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
CREATE TABLE job_table(time_ss CHAR(2),job_type VARCHAR(10),job_name VARCHAR(20));
-- Create stored procedure
CREATE OR REPLACE PROCEDURE job_proc IS
BEGIN
  INSERT INTO job_table VALUES (
  	TO_CHAR(SYSDATE, 'SS'),
  	'job',
  	'job example'
  	);
  COMMIT;
END;
/

DECLARE
  jobid BIGINT;
BEGIN
  DBMS_JOB.SUBMIT(jobid,
  	'begin job_proc; end;',
  	SYSDATE,
  	'SYSDATE+1'
  	);
  COMMIT;
  DBMS_OUTPUT.PUT_LINE('Job:'||jobid||' is created!'); 
END;
/

--result
Job:1681 is created!
```

## BROKEN

```plsql
DBMS_JOB.BROKEN(
	job	IN BIGINT,
    broken IN BOOLEAN,
    next_date IN DATE DEFAULT SYSDATE);
```

The BROKEN procedure is used to set the stop flag for a scheduled job. A stopped scheduled job will no longer be scheduled by the system. Please note that the BROKEN procedure cannot be applied to a scheduled job that is currently executing.

|Parameter |Description |
| :-------- | :----------------------------------------------------------- |
| job        | The object ID of the scheduled job, which can be queried through the DBA_JOBS/ALL_JOBS/USER_JOBS views. |
| broken     | Whether to stop the scheduled job; true means stop, false means do not stop. |
| next_date  | The time for the next execution of the scheduled job.                                     |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
-- Stop JOB
EXEC DBMS_JOB.BROKEN(1681,true);
COMMIT;

-- Restart the stopped JOB
EXEC DBMS_JOB.BROKEN(1681,false);
COMMIT;
```

## CHANGE

```plsql
DBMS_JOB.CHANGE(
	job IN BIGINT,
	what IN VARCHAR,
	next_date IN DATE,
	interval IN VARCHAR,
	instance IN INTEGER DEFAULT NULL,
	force IN BOOLEAN DEFAULT FALSE);
```

The CHANGE procedure is used to modify the related properties of a scheduled job, where using NULL as a parameter value indicates that the property should retain its original value.

|Parameter |Description |
| :-------- |:--------------------------------------------------------------------------------------------------------------------------------------|
| job        | The object ID of the scheduled job, which can be queried through the DBA_JOBS/ALL_JOBS/USER_JOBS views. |
| what       | The PL/SQL block to be executed by the scheduled job, must end with a semicolon.              |
| next_date  | The time for the next execution of the scheduled job; if the parameter value is in the past (based on the database server's operating system time), next_date will be modified to the current time.<br/> After modification, the scheduled job will be dispatched according to the modified next execution time; for example, setting this parameter to the current time (or a past time) will cause the scheduled job to execute immediately after modification. |
| interval   | Expression text to calculate the next execution time of the scheduled job; NULL indicates that the scheduled job will execute only once. The calculated time must be in the future or NULL. |
| instance   | The instance in a clustered environment where the scheduled job runs; NULL means that the job's instance will not be changed. |
| force      | When FALSE, the specified instance must be running; when TRUE, the instance can be any integer. |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
-- Change the execution interval of JOB to 1 hour
EXEC DBMS_JOB.CHANGE(1681,'begin job_proc; end;',NULL,'SYSDATE+1/24');
COMMIT;

EXEC DBMS_JOB.CHANGE(1681,'begin job_proc; end;',NULL,'SYSDATE+1/24', 3);
YAS-06806 job associated instance number 3 is not valid

EXEC DBMS_JOB.CHANGE(1681,'begin job_proc; end;',NULL,'SYSDATE+1/24', 1, TRUE);
COMMIT;
```

## INSTANCE

```plsql
DBMS_JOB.INSTANCE(
	job IN BIGINT,
	instance IN INTEGER,
	force IN BOOLEAN DEFAULT FALSE);
```

The INSTANCE procedure is used to set the instance associated with the scheduled job.

|Parameter |Description |
| :--- | :----------------------------------------------------------- |
| job        | The object ID of the scheduled job, which can be queried through the DBA_JOBS/ALL_JOBS/USER_JOBS views. |
| instance   | The instance in a clustered environment where the scheduled job runs. |
| force      | When FALSE, the specified instance must be running; when TRUE, any integer instance can be created. |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
EXEC DBMS_JOB.INSTANCE(1681, 1);
COMMIT;

EXEC DBMS_JOB.INSTANCE(1681, 1, TRUE);
COMMIT;
```

## INTERVAL

```plsql
DBMS_JOB.INTERVAL(
	job IN BIGINT,
	interval IN VARCHAR);
```

The INTERVAL procedure is used to modify the frequency of the scheduled job execution.

|Parameter |Description |
| :------- | :----------------------------------------------------------- |
| job        | The object ID of the scheduled job, which can be queried through the DBA_JOBS/ALL_JOBS/USER_JOBS views. |
| interval    | Expression text used to calculate the next execution time of the scheduled job; NULL means the scheduled job will execute only once. The calculated time must be in the future or NULL. |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
-- Change the execution interval of JOB to 2 hours
EXEC DBMS_JOB.INTERVAL(1681,'SYSDATE+1/12');
COMMIT;
```

## NEXT_DATE

```plsql
DBMS_JOB.NEXT_DATE(
	job IN BIGINT,
	next_date IN DATE);
```

The NEXT_DATE procedure is used to modify the next execution time of the scheduled job.

|Parameter |Description |
| :-------- |:------------------------------------------------------------------|
| job        | The object ID of the scheduled job, which can be queried through the DBA_JOBS/ALL_JOBS/USER_JOBS views. |
| next_date  | The next execution time of the scheduled job. If the parameter value is in the past, the next execution time of the scheduled job will not be modified to this parameter value; the scheduled job will continue to be dispatched according to its original next execution time. |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
-- Change the next execution time of JOB to 1 minute later
EXEC DBMS_JOB.NEXT_DATE(1681,SYSDATE+1/24/60);
COMMIT;
```

## RUN

```plsql
DBMS_JOB.RUN(
	job IN BIGINT,
	force IN BOOLEAN DEFAULT FALSE);
```

The RUN procedure is used to manually execute a scheduled job once. The status of the scheduled job will change to RUNNING.

|Parameter |Description |
| :---- | :----------------------------------------------------------- |
| job       | The object ID of the scheduled job, which can be queried through the DBA_JOBS/ALL_JOBS/USER_JOBS views. |
| force     | When FALSE, only the scheduled job of the current instance can be executed. When TRUE, scheduled jobs of non-current instances can also be executed. |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
EXEC DBMS_JOB.RUN(1681);
COMMIT;
```

## WHAT

```plsql
DBMS_JOB.WHAT(
	job IN BIGINT,
	what IN VARCHAR);
```

The WHAT procedure is used to modify the execution content of the scheduled job.

|Parameter |Description |
| :--- | :----------------------------------------------------------- |
| job       | The object ID of the scheduled job, which can be queried through the DBA_JOBS/ALL_JOBS/USER_JOBS views. |
| what      | The PL/SQL block to be executed by the scheduled job, must end with a semicolon. |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
EXEC DBMS_JOB.WHAT(1681,'begin job_proc; commit; end;');
COMMIT;
```

## REMOVE

```plsql
DBMS_JOB.REMOVE(
	job IN BIGINT);
```

The REMOVE procedure is used to delete a scheduled job that is not in the executed state. Once deleted, the scheduled job data can no longer be queried in the DBA_JOBS/ALL_JOBS/USER_JOBS views.

|Parameter |Description |
| :--- | :----------------------------------------------------------- |
| job       | The object ID of the scheduled job, which can be queried through the DBA_JOBS/ALL_JOBS/USER_JOBS views. |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
EXEC DBMS_JOB.REMOVE(1681);
COMMIT;
```

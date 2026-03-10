The DBMS_SCHEDULER package provides a set of built-in stored procedures for creating and managing [scheduled jobs](../PL Objects/Scheduled Jobs).

This advanced package is not suitable for ISC Distributed Cluster Deployment.

## CREATE\_JOB

```plsql
DBMS_SCHEDULER.CREATE_JOB (
	job_name            IN VARCHAR,
	job_type            IN VARCHAR,
	job_action          IN VARCHAR,
	number_of_arguments IN INTEGER DEFAULT 0,
	start_date          IN TIMESTAMP WITH TIME ZONE DEFAULT NULL,
	repeat_interval     IN VARCHAR DEFAULT NULL,
	end_date            IN TIMESTAMP WITH TIME ZONE DEFAULT NULL,
	job_class           IN VARCHAR DEFAULT 'DEFAULT_JOB_CLASS',
	enabled             IN BOOLEAN DEFAULT FALSE,
	auto_drop           IN BOOLEAN DEFAULT TRUE,
	comments            IN VARCHAR DEFAULT NULL);
```

The CREATE_JOB procedure is used to create a new scheduled job. Once successfully created, the scheduled job can be queried in the DBA_SCHEDULER_JOBS/ALL_SCHEDULER_JOBS/USER_SCHEDULER_JOBS views.

|Parameter |Description |
| :------------------ | :----------------------------------------------------------- |
| job_name            | The name of the scheduled job, which can be in the schema.job_name format and must comply with YashanDB's [object naming conventions](../../SQL Reference Manual/Basic SQL Elements/Identifiers). |
| job_type            | The type of job_action. 'PLSQL_BLOCK' represents an anonymous block, while 'STORED_PROCEDURE' represents a stored procedure. |
| job_action          | The PL/SQL text to be executed by the scheduled job, which can be a specific anonymous block or stored procedure, must end with a semicolon, and should match the job_type. |
| number_of_arguments | Reserved field, use default value.                               |
| start_date          | The time at which the scheduled job begins execution; NULL means it executes immediately. |
| repeat_interval     | Text expression used to calculate the next execution time of the scheduled job; NULL means the scheduled job executes only once. According to date/time arithmetic rules, the input interval for repeat_interval is in days, e.g., SYSDATE+1 means the next execution time is one day after the current time. The calculated time must be in the future or NULL. |
| end_date            | The time at which the scheduled job ceases execution; after this time, the job will not execute automatically. |
| job_class           | Reserved field, use default value.                               |
| enabled             | Indicates whether the scheduled job is active; defaults to false and inactive. Only active scheduled jobs will execute automatically. |
| auto_drop           | Whether to automatically delete the scheduled job after completion. <br> The scheduled job will be marked as completed if any of the following conditions are met:<br>* The current time has exceeded the scheduled job's end_date.<br>* The user has set the maximum execution count for the scheduled job, and the number of auto executions has reached the maximum.<br>* A non-repeating scheduled job (without a repeat_interval set) has executed once. |
| comments            | Descriptive information added to the scheduled job, defaults to NULL. |

***Example*** for Standalone Deployment and YAC Deployment

```plsql
EXEC DBMS_SCHEDULER.CREATE_JOB(
	'sche_example',
	'PLSQL_BLOCK',
	'begin insert into area select max(a.area_no)+1,''sche'',''sche example'' from area a; commit; end;',
	0,
	SYSDATE+10/24/60,
	'SYSDATE+1',
	NULL,
	'DEFAULT_JOB_CLASS',
	true,
	true,
	NULL
	);
```

<span id="runjob" name="runjob" class="yaslink"></span>

## RUN\_JOB

```plsql
DBMS_SCHEDULER.RUN_JOB(
	job_name            IN VARCHAR,
	use_current_session IN BOOLEAN DEFAULT TRUE);
```

The RUN_JOB procedure manually executes a scheduled job once. The status of the scheduled job changes to RUNNING, and manually executed jobs cannot be stopped by the STOP_JOB procedure.

|Parameter |Description |
|---------------------| ----------------------------------------------------- |
| job_name            | The name of the scheduled job, which can be in the schema.job_name format.             |
| use_current_session | When set to true, the scheduled job is manually executed in the current session; when set to false, it is executed in a separate background thread. |

***Example*** for Standalone Deployment and YAC Deployment

```plsql
EXEC DBMS_SCHEDULER.RUN_JOB('sales.sche_example');
```

## DISABLE

```plsql
DBMS_SCHEDULER.DISABLE(
	job_name         IN VARCHAR,
	force            IN BOOLEAN DEFAULT FALSE,
	commit_semantics IN VARCHAR DEFAULT 'STOP_ON_FIRST_ERROR');
```

The DISABLE procedure is used to deactivate a scheduled job that is not currently executing. The deactivated scheduled job will no longer be scheduled by the system.

|Parameter |Description |
| :--------------- | :---------------------------------------- |
| job_name            | The name of the scheduled job, which can be in the schema.job_name format. |
| force               | Reserved field, use default value.                    |
| commit_semantics    | Reserved field, use default value.                    |

***Example*** for Standalone Deployment and YAC Deployment

```plsql
EXEC DBMS_SCHEDULER.DISABLE('sales.sche_example');
```

## ENABLE

```plsql
DBMS_SCHEDULER.ENABLE(
	job_name         IN VARCHAR,
	commit_semantics IN VARCHAR DEFAULT 'STOP_ON_FIRST_ERROR');
```

The ENABLE procedure is used to activate a scheduled job.

|Parameter |Description |
| :--------------- | :---------------------------------------- |
| job_name            | The name of the scheduled job, which can be in the schema.job_name format. |
| commit_semantics    | Reserved field, use default value.                    |

***Example*** for Standalone Deployment and YAC Deployment

```plsql
EXEC DBMS_SCHEDULER.ENABLE('sales.sche_example');
```

## SET\_ATTRIBUTE

```plsql
DBMS_SCHEDULER.SET_ATTRIBUTE(
	job_name  IN VARCHAR,
	attribute IN VARCHAR,
	value     IN {BOOLEAN|DATE|TIMESTAMP|TIMESTAMP WITH LOCAL TIME ZONE|TIMESTAMP WITH TIME ZONE|VARCHAR|INTEGER},
	value2    IN VARCHAR DEFAULT NULL);
```

The SET_ATTRIBUTE procedure is used to set attributes of a scheduled job.

The end_date set for the scheduled job cannot be earlier than the start_date or the current time; otherwise, the scheduled job becomes inactive, and setting the end_date will not trigger the job.

|Parameter |Description |
| :-------- |:--------------------------------------------------------------------------------------------------------------------|
| job_name            | The name of the scheduled job, which can be in the schema.job_name format.                                            |
| attribute           | The name of the attribute to modify, which can be 'auto_drop', 'comments', 'end_date', 'job_action', 'repeat_interval', 'start_date', 'instance_id', 'next_date'. |
| value               | The value to modify for the attribute; its type must match the corresponding attribute type.                          |
| value2              | Syntax compatibility, has no practical meaning.                                                                         |

***Example*** for Standalone Deployment and YAC Deployment

```plsql
-- Set the execution frequency for the sche_example job
EXEC DBMS_SCHEDULER.SET_ATTRIBUTE('sche_example','repeat_interval','SYSDATE+0.003');

-- Set the end time for the sche_example job to be 60 minutes later
EXEC DBMS_SCHEDULER.SET_ATTRIBUTE('sche_example','end_date',SYSDATE+60/24/60);

-- Set the sche_example job to be automatically deleted after completion
EXEC DBMS_SCHEDULER.SET_ATTRIBUTE('sche_example','auto_drop',true);
```

## STOP\_JOB

```plsql
DBMS_SCHEDULER.STOP_JOB(
	job_name 			IN VARCHAR,
	force    			IN BOOLEAN DEFAULT FALSE,
	commit_semantics 	IN VARCHAR DEFAULT 'STOP_ON_FIRST_ERROR');
```

The STOP_JOB procedure is used to stop a currently running scheduled job, but cannot stop scheduled jobs that are manually executed through the [RUN_JOB procedure](#runjob).

|Parameter |Description |
| :-------- | :----------------------------------------------------------- |
| job_name            | The name of the scheduled job, which can be in the schema.job_name format; if setting multiple job names, separate with commas. |
| force               | Syntax compatibility, has no practical meaning.                    |
| commit_semantics    | Syntax compatibility, has no practical meaning.                    |

***Example*** for Standalone Deployment and YAC Deployment

```plsql

EXEC DBMS_SCHEDULER.STOP_JOB('sales.sche_example');

EXEC DBMS_SCHEDULER.STOP_JOB('sche_example, sales.sche_example2');
```

## DROP\_JOB

```plsql
DBMS_SCHEDULER.DROP_JOB(
	job_name         IN VARCHAR,
	force            IN BOOLEAN DEFAULT FALSE,
	defer            IN BOOLEAN DEFAULT FALSE,
	commit_semantics IN VARCHAR DEFAULT 'STOP_ON_FIRST_ERROR');
```

The DROP_JOB procedure is used to delete a scheduled job that is not currently executing.

|Parameter |Description |
| :--------------- | :---------------------------------------- |
| job_name            | The name of the scheduled job, which can be in the schema.job_name format. |
| force               | Reserved field, use default value.                    |
| defer               | Reserved field, use default value.                    |
| commit_semantics    | Reserved field, use default value.                    |

***Example*** for Standalone Deployment and YAC Deployment

```plsql
EXEC DBMS_SCHEDULER.DROP_JOB('sales.sche_example');
```

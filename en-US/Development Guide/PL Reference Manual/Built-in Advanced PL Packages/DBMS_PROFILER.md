DBMS_PROFILER is an Oracle-compatible PL code profiling tool that collects execution performance data from PL applications to perform line-level performance analysis.

The core functions of DBMS_PROFILER include:

- Execution count statistics: Records the number of times each line of code is executed
- Execution time measurement: Records execution time for each line of code in nanoseconds (total time, minimum time, maximum time)
- Code coverage analysis: Identifies which code lines are executed and which are not
- Performance bottleneck identification: Helps developers quickly locate the most time-consuming code segments

> **Note**:
>
> - The DBMS_PROFILER advanced package is not applicable to ISC Distributed Cluster Deployment.
>
> - Users who execute the DBMS_PROFILER advanced package must have the execute privilege on this advanced package.
>
> - The first step in using DBMS_PROFILER is to call the `CREATE_TABLES` procedure to create profiling data tables. Modifying the table structure is **not recommended**, as it may cause unnecessary errors.
>
> - Profiling sessions do not take effect in parallel execution scenarios. The collected results will not include data related to pipelined table functions (PIPELINED).
>
> - For external user-defined functions, profiling sessions only collect execution time and do not collect internal execution details.
>
> - Starting a profiling session will have a certain impact on performance. During a profiling session, performance data is first accumulated in memory. If the profiling duration is too long or the amount of executed code is very large, memory usage will continue to increase. You can call the `FLUSH_DATA` procedure to flush data to disk and free memory, but this will increase I/O overhead and may affect the performance of the profiled program.
>
> - The lifecycle of performance collection data is session-level. Memory is released when the session disconnects or when `STOP_PROFILER` is manually called.

## CREATE_TABLES

```plsql
DBMS_PROFILER.CREATE_TABLES (
   force_it IN BOOLEAN DEFAULT FALSE);
```

This sub-stored procedure creates profiling data tables, including three tables: PLSQL_PROFILER_RUNS, PLSQL_PROFILER_UNITS, and PLSQL_PROFILER_DATA.

| Parameter | Description |
|:---|:---|
| force_it | Whether to force rebuild. Default is FALSE, which returns an error if the data table already exists. When set to TRUE, if the data table already exists, it will be deleted and recreated. |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
-- Create profiling data tables
BEGIN
    DBMS_PROFILER.CREATE_TABLES();
END;
/

-- Force rebuild data tables
BEGIN
    DBMS_PROFILER.CREATE_TABLES(force_it => TRUE);
END;
/
```

## START_PROFILER

```plsql
DBMS_PROFILER.START_PROFILER (
   run_comment   IN VARCHAR := SYSDATE,
   run_comment1  IN VARCHAR := '',
   run_number    OUT BINARY_INTEGER)
 RETURN BINARY_INTEGER;

DBMS_PROFILER.START_PROFILER (
   run_comment  IN VARCHAR := SYSDATE,
   run_comment1 IN VARCHAR := '')
 RETURN BINARY_INTEGER;

DBMS_PROFILER.START_PROFILER (
   run_comment   IN VARCHAR := SYSDATE,
   run_comment1  IN VARCHAR := '',
   run_number    OUT BINARY_INTEGER);

DBMS_PROFILER.START_PROFILER (
   run_comment   IN VARCHAR := SYSDATE,
   run_comment1  IN VARCHAR := '');
```

This sub-stored function starts a profiling session, generates a new run ID, and initializes performance data collection. Returns 0 for success, and returns 1 for execution failure due to a parameter error.

| Parameter | Description |
|:---|:---|
| run_comment | Run comment, defaults to current timestamp. Maximum length is 2047 bytes. |
| run_comment1 | Additional comment, maximum length is 2047 bytes. Can be omitted, defaults to empty string. |
| run_number | When execution is successful, sets the unique identifier (RUNID) for this run. |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
DECLARE
    run_id BINARY_INTEGER;
    ret    BINARY_INTEGER;
BEGIN
    ret := DBMS_PROFILER.START_PROFILER('Performance test', '', run_id);
    DBMS_OUTPUT.PUT_LINE('Run ID: ' || run_id || ', Return: ' || ret);
END;
/
```

## STOP_PROFILER

```plsql
DBMS_PROFILER.STOP_PROFILER
 RETURN BINARY_INTEGER;

DBMS_PROFILER.STOP_PROFILER;
```

This sub-stored function stops the profiling session and writes the performance data in memory to database tables. Returns 0 for success, and returns 2 for execution failure due to data flush failure.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
DECLARE
    ret BINARY_INTEGER;
BEGIN
    -- Start profiling first
    DBMS_PROFILER.START_PROFILER('Test stop');

    -- Execute code to be profiled
    FOR i IN 1..100 LOOP
        NULL;
    END LOOP;

    -- Stop profiling
    ret := DBMS_PROFILER.STOP_PROFILER();
    DBMS_OUTPUT.PUT_LINE('Return: ' || ret);
END;
/
```

## PAUSE_PROFILER

```plsql
DBMS_PROFILER.PAUSE_PROFILER
 RETURN BINARY_INTEGER;

DBMS_PROFILER.PAUSE_PROFILER;
```

This sub-stored function pauses performance data collection. Code executed during the pause period will not be recorded. Returns 0 for success.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
DECLARE
    ret BINARY_INTEGER;
BEGIN
    -- Start profiling
    DBMS_PROFILER.START_PROFILER('Pause test');

    -- Record initial phase performance data
    FOR i IN 1..100 LOOP
        NULL;
    END LOOP;

    -- Pause profiling
    ret := DBMS_PROFILER.PAUSE_PROFILER();
    DBMS_OUTPUT.PUT_LINE('Paused. Return: ' || ret);

    -- This code will not be recorded
    FOR i IN 1..1000 LOOP
        NULL;
    END LOOP;

    -- Stop profiling
    DBMS_PROFILER.STOP_PROFILER();
END;
/
```

## RESUME_PROFILER

```plsql
DBMS_PROFILER.RESUME_PROFILER
 RETURN BINARY_INTEGER;

DBMS_PROFILER.RESUME_PROFILER;
```

This sub-stored function resumes performance data collection. Returns 0 for success.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
DECLARE
    ret BINARY_INTEGER;
BEGIN
    -- Start profiling
    DBMS_PROFILER.START_PROFILER('Resume test');

    -- Record initial phase performance data
    FOR i IN 1..100 LOOP
        NULL;
    END LOOP;

    -- Pause profiling
    DBMS_PROFILER.PAUSE_PROFILER();

    -- Resume profiling
    ret := DBMS_PROFILER.RESUME_PROFILER();
    DBMS_OUTPUT.PUT_LINE('Resumed. Return: ' || ret);

    -- Continue recording performance data
    FOR i IN 1..100 LOOP
        NULL;
    END LOOP;

    -- Stop profiling
    DBMS_PROFILER.STOP_PROFILER();
END;
/
```

## FLUSH_DATA

```plsql
DBMS_PROFILER.FLUSH_DATA
 RETURN BINARY_INTEGER;

DBMS_PROFILER.FLUSH_DATA;
```

This sub-stored function flushes performance data from memory to database tables and releases the memory of flushed data. Returns 0 for success, and returns 2 for execution failure due to data flush failure.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
DECLARE
    ret BINARY_INTEGER;
BEGIN
    -- Start profiling
    DBMS_PROFILER.START_PROFILER('Flush test');

    -- Execute long-running code
    FOR outer_i IN 1..10 LOOP
        FOR inner_i IN 1..10000 LOOP
            NULL;
        END LOOP;

        -- Flush data to disk after each loop iteration, release memory
        ret := DBMS_PROFILER.FLUSH_DATA();
        DBMS_OUTPUT.PUT_LINE('Flushed. Return: ' || ret);
    END LOOP;

    -- Stop profiling
    DBMS_PROFILER.STOP_PROFILER();
END;
/
```

## GET_VERSION

```plsql
DBMS_PROFILER.GET_VERSION (
   major  OUT BINARY_INTEGER,
   minor  OUT BINARY_INTEGER);
```

GET_VERSION retrieves version information for the DBMS_PROFILER package.

| Parameter | Description |
|:---|:---|
| major | Returns the major version number, current value is 1. |
| minor | Returns the minor version number, current value is 0. |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
DECLARE
    major INTEGER;
    minor INTEGER;
BEGIN
    DBMS_PROFILER.GET_VERSION(major, minor);
    DBMS_OUTPUT.PUT_LINE('Version: ' || major || '.' || minor);
END;
/
```

## INTERNAL_VERSION_CHECK

```plsql
DBMS_PROFILER.INTERNAL_VERSION_CHECK
 RETURN BINARY_INTEGER;
```

This sub-stored function performs internal version compatibility check. Returns 0 for success.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
DECLARE
    ret BINARY_INTEGER;
BEGIN
    ret := DBMS_PROFILER.INTERNAL_VERSION_CHECK();
    DBMS_OUTPUT.PUT_LINE('Return: ' || ret);
END;
/
```

## Profiling Data Tables

Performance analysis data is stored in the following three tables:

### PLSQL_PROFILER_RUNS

Stores run information for each profiling session.

| Column | Type | Description |
| :---------- | :---------- | :----------------------------------------------------------- |
| RUNID | NUMBER | Unique run identifier, primary key. |
| RELATED_RUN | NUMBER | Related run ID, currently fixed to NULL. |
| RUN_OWNER | VARCHAR2(128) | User who started the run. |
| RUN_DATE | DATE | Run start time. |
| RUN_COMMENT | VARCHAR2(2047) | Run comment provided by the user. |
| RUN_TOTAL_TIME | NUMBER | Total run duration in nanoseconds. |
| RUN_SYSTEM_INFO | VARCHAR2(2047) | System information, currently fixed to NULL. |
| RUN_COMMENT1 | VARCHAR2(2047) | Additional comment. |
| SPARE1 | VARCHAR2(256) | Reserved field. |

### PLSQL_PROFILER_UNITS

Stores profiling information for each library unit. The primary key is (RUNID, UNIT_NUMBER).

| Column | Type | Description |
| :---------- | :---------- | :----------------------------------------------------------- |
| RUNID | NUMBER | Related run ID, foreign key. |
| UNIT_NUMBER | NUMBER | Library unit internal number, starting from 1 and incrementing. |
| UNIT_TYPE | VARCHAR2(128) | Library unit type, including PACKAGE SPEC, PACKAGE BODY, TYPE BODY, FUNCTION, PROCEDURE, ANONYMOUS BLOCK. |
| UNIT_OWNER | VARCHAR2(128) | Library unit owner. |
| UNIT_NAME | VARCHAR2(128) | Library unit name. |
| UNIT_TIMESTAMP | DATE | Unit timestamp. |
| TOTAL_TIME | NUMBER | Unit total duration in nanoseconds. |
| SPARE1 | NUMBER | Reserved field, currently fixed to NULL. |
| SPARE2 | NUMBER | Reserved field, currently fixed to NULL. |


### PLSQL_PROFILER_DATA

Stores performance data for each line of code. The primary key is (RUNID, UNIT_NUMBER, LINE#).

| Column | Type | Description |
| :---------- | :---------- | :----------------------------------------------------------- |
| RUNID | NUMBER | Related run ID. |
| UNIT_NUMBER | NUMBER | Library unit number. |
| LINE# | NUMBER | Source code line number. |
| TOTAL_OCCUR | NUMBER | Execution count. |
| TOTAL_TIME | NUMBER | Total execution time in nanoseconds. |
| MIN_TIME | NUMBER | Minimum execution time in nanoseconds. |
| MAX_TIME | NUMBER | Maximum execution time in nanoseconds. |
| SPARE1 | NUMBER | SQL execution time, for example, executing DML statements or opening cursors, in nanoseconds. |
| SPARE2 | NUMBER | Sub-call execution time, for example, executing a subprocedure, jumping to exception handling, or variable initialization when the begin statement is executed, in nanoseconds. |
| SPARE3 | NUMBER | Reserved field, currently fixed to NULL. |
| SPARE4 | NUMBER | Reserved field, currently fixed to NULL. |
| LINE_TYPE | VARCHAR2(64) | Line type. |
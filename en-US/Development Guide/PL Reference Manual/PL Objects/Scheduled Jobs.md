A scheduled job (JOB) is a type of PL object in the database.

Jobs are not suitable for ISC Distributed Cluster Deployment.

The system provides the following built-in advanced packages for creating and managing jobs:

- [DBMS_JOB](../Built-in Advanced PL Packages/DBMS_JOB)
- [DBMS_SCHEDULER](../Built-in Advanced PL Packages/DBMS_SCHEDULER)

A job consists of the following three basic elements:

- A unique identifier for the JOB
- The task to be executed by the JOB
- The execution time and frequency of the JOB

DBMS_JOB provides a series of stored procedures for direct operation management of a JOB based on the above three elements. DBMS_SCHEDULER offers more rich and flexible functionalities compared to DBMS_JOB, such as setting task names, task end times, and automatic deletion after completion.

After invoking advanced packages to perform various operations for creating or managing jobs, users can monitor the execution status and various attributes of jobs through the following configuration parameters or system views:

**JOB_QUEUE_PROCESSES**

This configuration parameter specifies the number of threads for running JOBs in the background. When JOB_QUEUE_PROCESSES=0, no JOBs in the system will be executed automatically. When the number of JOBs that need to be executed automatically at a given time exceeds the current number of available JOB threads, the JOBs must wait in a queue.

**JOB/SCHEDULER Views**

JOB Views: DBA_JOBS/ALL_JOBS/USER_JOBS.

SCHEDULER Views: DBA_SCHEDULER_JOBS/ALL_SCHEDULER_JOBS/USER_SCHEDULER_JOBS.

To view various attributes of jobs and track the execution status of jobs, for example, to check the task queue under the current user that is scheduled (not stopped, or the status is valid and within the validity period):

***Example*** for Standalone Deployment and YAC Deployment

```plsql
SELECT job, next_date, failures 
FROM USER_JOBS
WHERE broken='N';
  JOB NEXT_DATE                            FAILURES 
----- -------------------------------- ------------ 
 1616 2022-06-22 22:44:33.000000                  0
 1617 2022-06-22 22:44:38.000000                  0
 1618 2022-06-21 22:55:06.000000                  0
 
SELECT job_name,next_run_date,failure_count
FROM USER_SCHEDULER_JOBS
WHERE enabled=true
AND (START_DATE IS NULL OR SYSDATE>START_DATE)
AND (END_DATE IS NULL OR SYSDATE<END_DATE);
JOB_NAME             NEXT_RUN_DATE                    FAILURE_COUNT 
-------------------- -------------------------------- ------------- 
DBMS_JOB$_1616       2022-06-22 22:44:33.000000                   0
DBMS_JOB$_1617       2022-06-22 22:44:38.000000                   0
SCHE_EXAMPLE         2022-06-21 22:55:06.000000                   0
```

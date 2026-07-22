The DBMS_IJOB package provides a set of built-in stored procedures for creating and managing [scheduled jobs](../PL Objects/Scheduled Jobs) for a specified user. It is important to note that all operations performed on scheduled jobs by invoking these stored procedures will take effect only after executing COMMIT.

This advanced package is not suitable for ISC Distributed Cluster Deployment.

## SUBMIT

```plsql
DBMS_IJOB.SUBMIT(
    JOB OUT BINARY_INTEGER
	LUSER IN VARCHAR2,
	PUSER IN VARCHAR2,
    CUSER IN VARCHAR2 DEFAULT CURUSER,
    NEXT_DATE IN DATE DEFAULT SYSDATE,
    INTERVAL IN VARCHAR DEFAULT NULL,
    BROKEN IN BOOLEAN DEFAULT FALSE,
    WHAT IN VARCHAR2 NOT NULL
	CS_LAB IN VARCHAR2,
	CS_HI IN VARCHAR2,
	CS_LO IN VARCHAR2,
	NLSENV IN VARCHAR2,
	ENV IN VARCHAR2,
);
```

The SUBMIT procedure can create a new scheduled job for a specified user. Successfully created scheduled jobs can be queried in the DBA_JOBS/ALL_JOBS/USER_JOBS views.

> **Note**: 
>
> This procedure can only be called and executed by the following users:
>
> - SYS user
> - Users with DBA role
> - Users with all privileges privilege

|Parameter |Description |
| :-------- | :----------------------------------------------------------- |
| JOB       | The task number of the created job                        |
| LUSER     | Optional, does not take effect                             |
| PUSER     | Optional, does not take effect                             |
| CUSER     | The role that executes the task, defaults to the current logged-in user |
| NEXT_DATE | The time for the next execution of the scheduled job, defaults to immediate execution |
| INTERVAL  | Text expression used to calculate the next execution time of the scheduled job; NULL indicates that the job will only execute once. The time calculated by the expression must be in the future or NULL |
| BROKEN    | Whether the job is broken, defaults to FALSE               |
| WHAT      | PL text that the scheduled job will execute, can be an anonymous block or a stored procedure, must end with a semicolon |
| CS_LAB    | Optional, does not take effect                             |
| CS_HI     | Optional, does not take effect                             |
| CS_LO     | Optional, does not take effect                             |
| NLSENV    | Optional, does not take effect                             |
| ENV       | Optional, does not take effect                             |

***Example*** for Standalone/YAC/Distributed Cluster Deployment
```plsql
-- Create a stored procedure under the SALES user
CREATE TABLE tbl_job(id VARCHAR2(30), name VARCHAR2(30));
CREATE OR REPLACE PROCEDURE proce_t IS
BEGIN
INSERT INTO tbl_job(id, name) 
VALUES('1', TO_CHAR(SYSDATE, 'yyyy-mm-dd hh24:mi:ss'));
COMMIT;
END proce_t;
/

-- Execute as SYS user to create JOB for SALES user
DECLARE
    job_id INT;
BEGIN
    SYS.DBMS_IJOB.SUBMIT(
    job_id,
 cuser=>'SALES',
 INTERVAL=> 'sysdate+1/24/60',
 what=> 'proce_t;');
COMMIT;
END;
/

-- Check the job execution results under the SALES user: insert one record every minute
SELECT ID,NAME FROM tbl_job;
ID                                NAME                              
--------------------------------- --------------------------------- 
1                                 2022-11-08 19:50:57              
1                                 2022-11-08 19:51:57              
1                                 2022-11-08 19:52:57              
1                                 2022-11-08 19:53:57        

-- Delete JOB under the SALES user (retrieve the JOB ID from the user_jobs view)
exec DBMS_JOB.REMOVE(1925);
commit;
```

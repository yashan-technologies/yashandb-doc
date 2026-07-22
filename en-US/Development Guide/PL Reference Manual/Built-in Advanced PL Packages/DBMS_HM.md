The DBMS_HM package provides a set of built-in stored procedures/functions for implementing health check-related functionality in YashanDB.

## RUN_CHECK

```plsql
DBMS_HM.RUN_CHECK (
	check_name   IN   VARCHAR,
	run_name     IN   VARCHAR DEFAULT 'NULL',
    input_params IN   VARCHAR DEFAULT 'NULL');
```

RUN_CHECK is a stored procedure that executes a health check by running the specified checker with the provided parameters.

|Parameter |Description |
| :------------------ | :--------|
| check_name          | The name of the checker to be called. All checker information in the current system can be queried through the v$hm_check view. |
| run_name            | A unique name specified by the user to identify this check run. If it is NULL, the system creates a default name based on the current health check ID. This name can be viewed in the v$hm_run view. |
| input_params        | The parameter information required by the checker, composed of name=value pairs, separated by `;` for multiple parameters. (Example: The possible input parameters for the 'Data Block Integrity Check' checker may include: 'BLC_DF_NUM=1;BLC_BL_NUM=23456')<br>Each checker has clearly defined input parameters associated with it. This information, along with their types, default values, and descriptions can be obtained from the v$hm_check_param view. (Example: To query the parameter information for the 'Data Block Integrity Check' checker: SELECT a.* FROM v$hm_check_param a, v$hm_check b WHERE a.check_id = b.id AND b.name = 'Data Block Integrity Check';) |

***Example***

```plsql
BEGIN
  DBMS_HM.RUN_CHECK('Redo File Check','hm1','RF_NUM=1');
END;
/
```

## GET_RUN_REPORT

```plsql
DBMS_HM.GET_RUN_REPORT (
	run_name IN VARCHAR);
```

GET_RUN_REPORT is a function that returns the report of the specified checker run.

|Parameter |Description |
| :------------------ | :--------|
| run_name            | The name of the check run. Current check run information can be viewed in the v$hm_run view. |

***Example***

```plsql
SELECT DBMS_HM.GET_RUN_REPORT('hm1') FROM dual;
DBMS_HM.GET_RUN_REPO                                             
---------------------------------------------------------------- 
 Run Name                    : hm1
 Run Id                      : 3
 Check Name                  : Redo File Check
 Mode                        : MANUAL
 status                      : COMPLETED
 Start Time                  : 2022-07-19 01:50:27
 End Time                    : 2022-07-19 01:50:27
 Error Encountered           : 0
 Source Incident Id          : 0
 Number of Incidents Created : 0

Input Parameters for the Run
 RF_NUM=1
```

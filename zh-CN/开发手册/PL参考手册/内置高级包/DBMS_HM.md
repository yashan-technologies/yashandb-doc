DBMS_HM包提供了一组内置的存储过程/函数，用于实现YashanDB的健康检查相关功能。

## RUN_CHECK

```plsql
DBMS_HM.RUN_CHECK (
	check_name   IN   VARCHAR,
	run_name     IN   VARCHAR DEFAULT 'NULL',
    input_params IN   VARCHAR DEFAULT 'NULL');
```

RUN_CHECK为一个存储过程，通过给定参数运行指定的检查器，执行健康检查。

|  参数| 描述|
| :------------------ | :--------|
|check_name           |要调用的检查器名称。通过v$hm_check视图可以查询到当前系统中所有的检查器信息。|
|run_name             |用户指定的唯一标识此检查运行的名称，为NULL时，系统根据当前健康检查的ID创建一个默认名称。通过v$hm_run视图可以查看该名称信息。|
|input_params         |检查器要求的参数信息，由名称=值对组成，多参数使用`;`分隔。（示例：'Data Block Integrity Check'检查器可能的输入参数：'BLC_DF_NUM=1;BLC_BL_NUM=23456'）<br>每个检查器都具有与之关联的明确定义的输入参数。这些输入参数及其类型、默认值和描述可以通过v$hm_check_param视图获取。（示例：查询'Data Block Integrity Check' 检查器的参数信息：SELECT a.* FROM v$hm_check_param a, v$hm_check b WHERE a.check_id = b.id AND b.name = 'Data Block Integrity Check';）|

示例

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

GET_RUN_REPORT为一个函数，返回指定检查器运行的报告。

|  参数| 描述|
| :------------------ | :--------|
|check_name           |检查运行的名称。通过v$hm_run视图可以查看当前所有运行的检查信息。|

示例

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

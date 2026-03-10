DBMS_JOB包提供了一组内置的存储过程，用于创建和管理[定时任务](../PL对象/定时任务)。需要注意的是，调用这些存储过程所执行的对定时任务的所有操作，均只有在执行COMMIT后才会生效。

该高级包不适用于存算一体分布式集群部署。

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

SUBMIT程序用于创建一个新的定时任务，成功创建的定时任务将可以在DBA_JOBS/ALL_JOBS/USER_JOBS视图中查询。

|  参数| 描述|
| :-------- |:----------------------------------------------------------------------------|
| job       | 系统为定时任务分配的对象ID，BIGINT类型。                                                    |
| what      | 定时任务要执行的PL文本，可以是匿名块或者存储过程，必须以分号结束。                                          |
| next_date | 定时任务下次执行的时间。                                                                |
| interval  | 用于计算定时任务下次执行时间的表达式文本，计算出的时间必须为将来时间或NULL。<br/>若时间为NULL则表示定时任务只执行一次且执行完后直接删除。 |
| no_parse  | 标识在创建定时任务时，是否需要解析what指定的PL文本，true表示不解析，false表示会解析校验。                        |
| instance  | 定时任务执行的实例，默认值0表示在任意实例上执行。                                                   |
| force     | 为FALSE时，设置的instance必须是正在运行的；为TRUE时，instance可以设置为任意整数。                        |

说明：

- 共享集群部署中通过查询V$INSTANCE视图的INSTANCE_NUMBER字段可获得所在实例的instance值，其他部署模式下的instance值恒为1。
- 单机部署下输入非1的instance值在force=true时可以成功创建定义定时任务，但该任务并不会被执行。

示例（单机、共享集群部署）

```plsql
CREATE TABLE job_table(time_ss CHAR(2),job_type VARCHAR(10),job_name VARCHAR(20));
--创建存储过程
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

BROKEN程序用于设置定时任务的停止标志，被停止的定时任务将不再被系统进行任务调度，需要注意的是，BROKEN程序不能作用于一个正在执行的定时任务。

|  参数| 描述|
| :-------- | :----------------------------------------------------------- |
| job       | 定时任务的对象ID，可以通过DBA_JOBS/ALL_JOBS/USER_JOBS视图查询。 |
| broken    | 是否停止定时任务，true表示停止，false表示不停止。            |
| next_date | 定时任务下次执行的时间。                                     |

示例（单机、共享集群部署）

```plsql
--停止JOB
EXEC DBMS_JOB.BROKEN(1681,true);
COMMIT;

--重启被停止的JOB
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

CHANGE程序用于修改定时任务的相关属性，其中，使用NULL参数值表示对属性仍保持原值。

|  参数| 描述|
| :-------- |:--------------------------------------------------------------------------------------------------------------------------------------|
| job       | 定时任务的对象ID，可以通过DBA_JOBS/ALL_JOBS/USER_JOBS视图查询。                                                                                        |
| what      | 定时任务要执行的PL文本，可以是匿名块或者存储过程，必须以分号结束。                                                                                                    |
| next_date | 定时任务下次执行的时间，若参数值为过去时间（以数据库所在服务器操作系统时间为基准），会将next_date修改为当前时间。<br/>修改完成后，该定时任务会按修改后的下次执行时间进行调度，例如将该参数设置为当前时间（或过去时间），在修改完成后会立即执行一次定时任务。 |
| interval  | 表达式文本，用于计算定时任务下次执行的时间，表达式为NULL表示定时任务只执行一次。通过表达式计算出的时间必须为将来时间或者NULL。                                                                   |
| instance  | 定时任务在集群环境下执行的实例，默认值NULL表示不会更改job的instance。                                                                                            |
| force     | 为FALSE时，设置的instance必须是正在运行的；为TRUE时，instance可以设置为任意整数。                                                                                 |

示例（单机、共享集群部署）

```plsql
--修改JOB的执行间隔为1小时
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

INSTANCE程序用于设置定时任务关联的实列。

|  参数| 描述|
| :--- | :----------------------------------------------------------- |
| job  | 定时任务的对象ID，可以通过DBA_JOBS/ALL_JOBS/USER_JOBS视图查询。 |
| instance  | 定时任务在集群环境下执行的实例。 |
| force     | 为FALSE时，设置的instance必须是正在运行的。为TRUE时，任意整数的instance都可以创建。 |

示例（单机、共享集群部署）

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

INTERVAL程序用于修改定时任务执行的频率。

|  参数| 描述|
| :------- | :----------------------------------------------------------- |
| job      | 定时任务的对象ID，可以通过DBA_JOBS/ALL_JOBS/USER_JOBS视图查询。 |
| interval | 表达式文本，用于计算定时任务下次执行的时间，表达式为NULL表示定时任务只执行一次。通过表达式计算出的时间必须为将来时间或者NULL。 |

示例（单机、共享集群部署）

```plsql
--修改JOB的执行间隔为2小时
EXEC DBMS_JOB.INTERVAL(1681,'SYSDATE+1/12');
COMMIT;
```

## NEXT\_DATE

```plsql
DBMS_JOB.NEXT_DATE(
	job IN BIGINT,
	next_date IN DATE);
```

NEXT_DATE程序用于修改定时任务下次执行的时间。

|  参数| 描述|
| :-------- |:------------------------------------------------------------------|
| job       | 定时任务的对象ID，可以通过DBA_JOBS/ALL_JOBS/USER_JOBS视图查询。                    |
| next_date | 定时任务下次执行的时间。当参数值为过去时间时，不会将定时任务下次执行的时间修改为该参数值，即定时任务按原来的下次执行时间进行调度。 |

示例（单机、共享集群部署）

```plsql
--修改JOB的下次执行时间为1分钟后
EXEC DBMS_JOB.NEXT_DATE(1681,SYSDATE+1/24/60);
COMMIT;
```

## RUN

```plsql
DBMS_JOB.RUN(
	job IN BIGINT,
	force IN BOOLEAN DEFAULT FALSE);
```

RUN程序用于手动执行一次定时任务。定时任务的状态会变为RUNNING。

|  参数| 描述|
| :---- | :----------------------------------------------------------- |
| job   | 定时任务的对象ID，可以通过DBA_JOBS/ALL_JOBS/USER_JOBS视图查询。 |
| force | 为FALSE时只能执行当前实例的定时任务。为TRUE时，可以执行非当前实例的定时任务。  |

示例（单机、共享集群部署）

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

WHAT程序用于修改定时任务的执行内容。

|  参数| 描述|
| :--- | :----------------------------------------------------------- |
| job  | 定时任务的对象ID，可以通过DBA_JOBS/ALL_JOBS/USER_JOBS视图查询。 |
| what | 定时任务要执行的PL文本，可以是匿名块或者存储过程，必须以分号结束。 |

示例（单机、共享集群部署）

```plsql
EXEC DBMS_JOB.WHAT(1681,'begin job_proc; commit; end;');
COMMIT;
```

## REMOVE

```plsql
DBMS_JOB.REMOVE(
	job IN BIGINT);
```

REMOVE程序用于删除一个非执行状态下的定时任务，执行删除后将无法在DBA_JOBS/ALL_JOBS/USER_JOBS视图中查询到该定时任务数据。

|  参数| 描述|
| :--- | :----------------------------------------------------------- |
| job  | 定时任务的对象ID，可以通过DBA_JOBS/ALL_JOBS/USER_JOBS视图查询。 |

示例（单机、共享集群部署）

```plsql
EXEC DBMS_JOB.REMOVE(1681);
COMMIT;
```

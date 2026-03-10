DBMS_SCHEDULER包提供了一组内置的存储过程，用于创建和管理[定时任务](../PL对象/定时任务)。

该高级包不适用于分布式部署。

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

CREATE_JOB程序用于创建一个新的定时任务，成功创建的定时任务将可以在DBA_SCHEDULER_JOBS/ALL_SCHEDULER_JOBS/USER_SCHEDULER_JOBS视图中查询。

| 参数                | 描述                                                         |
| :------------------ | :----------------------------------------------------------- |
| job_name            | 定时任务名称，可以为schema.job_name格式，且需符合YashanDB的[对象命名规范](../../SQL参考手册/基本SQL元素/标识符)。 |
| job_type            | job_action的类型。'PLSQL_BLOCK'表示匿名块，'STORED_PROCEDURE'表示存储过程。 |
| job_action          | 定时任务要执行的PL文本，可以是具体的匿名块或存储过程，必须以分号结束，且应当与job_type匹配。 |
| number_of_arguments | 保留字段，使用缺省值。                                       |
| start_date          | 定时任务开始执行的时间，NULL表示立即执行。                   |
| repeat_interval     | 表达式文本，用于计算定时任务下次执行的时间，表达式为NULL表示定时任务只执行一次。根据日期时间型算术计算规则，repeat_interval参数所输入的间隔以天为单位，例如SYSDATE+1表示下次执行任务时间为当前时间的后一天。通过表达式计算出的时间必须为将来时间或NULL。 |
| end_date            | 定时任务执行结束的时间，超过该时间后定时任务将不再自动执行。 |
| job_class           | 保留字段，使用缺省值。                                       |
| enabled             | 定时任务是否生效，默认为false，不生效。只有生效的定时任务才会自动执行。 |
| auto_drop           | 当定时任务完成后是否自动删除。<br>满足以下条件之一时，定时任务会被标记为完成：<br>* 当前时间已超过定时任务的end_date。<br>* 用户设置了定时任务的最大执行次数，且定时任务自动执行的次数已达到最大执行次数。<br>* 不重复执行（未设置repeat_interval）的定时任务已执行了一次。 |
| comments            | 为定时任务添加的描述信息，默认为NULL。                       |

示例（单机、共享集群部署）

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

RUN_JOB程序会手动执行一次定时任务，定时任务的状态会变为RUNNING，手动执行的任务无法通过STOP_JOB程序停止。

| 参数                  | 描述                                                  |
|---------------------| ----------------------------------------------------- |
| job_name            | 定时任务名称，可以为schema.job_name格式。             |
| use_current_session | 当参数值为true时，在当前session下手动执行定时任务；当参数值为false时，另起一个后台线程手动执行定时任务。|

示例（单机、共享集群部署）

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

DISABLE程序用于使一个非执行状态下的定时任务失效，失效后的定时任务将不再被系统调度。

| 参数             | 描述                                      |
| :--------------- | :---------------------------------------- |
| job_name         | 定时任务名称，可以为schema.job_name格式。 |
| force            | 保留字段，使用缺省值。                    |
| commit_semantics | 保留字段，使用缺省值。                    |

示例（单机、共享集群部署）

```plsql
EXEC DBMS_SCHEDULER.DISABLE('sales.sche_example');
```

## ENABLE

```plsql
DBMS_SCHEDULER.ENABLE(
	job_name         IN VARCHAR,
	commit_semantics IN VARCHAR DEFAULT 'STOP_ON_FIRST_ERROR');
```

ENABLE程序用于使一个定时任务生效。

| 参数             | 描述                                      |
| :--------------- | :---------------------------------------- |
| job_name         | 定时任务名称，可以为schema.job_name格式。 |
| commit_semantics | 保留字段，使用缺省值。                    |

示例（单机、共享集群部署）

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

SET_ATTRIBUTE程序用于设置定时任务的属性。

为定时任务设置的end_date不能早于start_date或当前时间，否则定时任务失效，设置完end_date后不会触发执行该定时任务。

| 参数      | 描述                                                                                                                  |
| :-------- |:--------------------------------------------------------------------------------------------------------------------|
| job_name  | 定时任务名称，可以为schema.job_name格式。                                                                                        |
| attribute | 要修改的属性名称，可以为'auto_drop','comments','end_date','job_action','repeat_interval','start_date','instance_id','next_date'。 |
| value     | 要修改的属性值，其类型需与attribute对应类型一致。                                                                                       |
| value2    | 语法兼容，无实际含义。                                                                                                         |

示例（单机、共享集群部署）

```plsql
-- 设置sche_example任务的执行频率
EXEC DBMS_SCHEDULER.SET_ATTRIBUTE('sche_example','repeat_interval','SYSDATE+0.003');

-- 设置sche_example任务的结束时间为60分钟后
EXEC DBMS_SCHEDULER.SET_ATTRIBUTE('sche_example','end_date',SYSDATE+60/24/60);

-- 设置sche_example任务为完成后自动删除
EXEC DBMS_SCHEDULER.SET_ATTRIBUTE('sche_example','auto_drop',true);
```

## STOP\_JOB

```plsql
DBMS_SCHEDULER.STOP_JOB(
	job_name 			IN VARCHAR,
	force    			IN BOOLEAN DEFAULT FALSE,
	commit_semantics 	IN VARCHAR DEFAULT 'STOP_ON_FIRST_ERROR');
```

STOP_JOB程序用于停止正在运行的定时任务，但无法停止通过[RUN_JOB程序](#runjob)手动执行的定时任务。

| 参数      | 描述                                                         |
| :-------- | :----------------------------------------------------------- |
| job_name  | 定时任务名称，可以为schema.job_name格式，如果是设置多个定时任务名称，中间用逗号分隔。 |
| force     | 语法兼容，无实际含义。                                        |
| commit_semantics     | 语法兼容，无实际含义。                             |

示例（单机、共享集群部署）

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

DROP_JOB程序用于删除一个非执行状态下的定时任务。

| 参数             | 描述                                      |
| :--------------- | :---------------------------------------- |
| job_name         | 定时任务名称，可以为schema.job_name格式。 |
| force            | 保留字段，使用缺省值。                    |
| defer            | 保留字段，使用缺省值。                    |
| commit_semantics | 保留字段，使用缺省值。                    |

示例（单机、共享集群部署）

```plsql
EXEC DBMS_SCHEDULER.DROP_JOB('sales.sche_example');
```

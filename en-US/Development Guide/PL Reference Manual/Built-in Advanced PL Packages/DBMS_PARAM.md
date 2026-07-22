The DBMS_PARAM package provides a set of variables, functions, and stored procedures to set recommended parameters for the database. The recommended parameters adjust the configuration of some key parameters to avoid low-level errors as much as possible, but do not guarantee to resolve all configuration issues. Specific scenarios still require further tuning.

Users with ALTER SYSTEM privilege can view and apply recommended parameters using this advanced package according to business type and system resource status.

## OPTIMIZE

```plsql
DBMS_PARAM.OPTIMIZE (
	apply_parameter     BOOL,
	table_type          VARCHAR,
	os_memory_limit     NUMBER,
	os_cpu_limit        NUMBER,
	data_path           VARCHAR,
	redo_path           VARCHAR,
	scene               VARCHAR,
	scale_factor        NUMBER
);
```

This stored procedure is used to generate recommended parameters, and the old recommended parameters will be overridden.

All parameters of this stored procedure have default values, requiring a minimum of 0 parameters and a maximum of 8 parameters to be input.

> **Caution**:
>
> - During the generation of recommended parameters, the system will test the disk performance where the datafile and redofile are located. Therefore, it is recommended to execute it when there is no business activity to avoid affecting the accuracy of the recommended parameters.

|Parameter |Description |
| :--- | :---- |
| apply_parameter  | Whether to immediately write the generated recommended parameters to the configuration file, default is FALSE. If set to TRUE, the parameters will be immediately written to the configuration file, but the database must be restarted for the relevant parameters to take effect. |
| table_type      | The main business table type, optional [HEAP,TAC,LSC], default is HEAP. |
| os_memory_limit | The upper limit of the percentage of server memory available for the database to use, defaults to 100, which allocates all memory. It is recommended to set according to requirements. |
| os_cpu_limit    | The upper limit of the percentage of the server's CPU that can be used by the database, default is 100, which allocates all CPU. |
| data_path       | The path where the datafile is located, default is '', the system will automatically obtain the datafile path.<br>NOMOUNT mode default is $YASDB_DATA/dbfiles.<br>MOUNT and OPEN modes default to a random datafile path. |
| redo_path       | The path where the redofile is located, default is '', the system will automatically obtain the redofile path.<br>NOMOUNT mode default is $YASDB_DATA/dbfiles.<br>MOUNT and OPEN modes default to a random redofile path. |
| scene           | The scenario for parameter tuning, default is NORMAL<br/>* In Standalone Deployment, YAC Deployment or Distributed Cluster Deployment, optional values are [NORMAL, BATCH]<br />* In ISC Distributed Cluster Deployment, optional values are [NORMAL, TPCH, AIM]<br /><br />The applicable scenarios for each parameter value are as follows:<br />* NORMAL: General mixed workload scenario, applicable to all table types. <br/>* BATCH: Batch processing scenario, can only be set to BATCH when the table type is HEAP. When the business mainly uses HEAP tables for OLTP and experiences poor performance during periodic batch scenarios, consider using the BATCH scenario for tuning<br />* TPCH: Complex analytical query scenario, can only be set to TPCH when the table type is LSC. When using TPCH scenario for tuning recommendation, ensure all nodes execute the parameter recommendation; otherwise, restart may fail due to mismatched connection parameters<br />* AIM: Full memory optimization scenario, can be set to AIM when YashanDB is deployed as a distributed in-memory database |
| scale_factor    | Data volume for test scenarios such as TPCH (unit: GB). Value range is [1, 100000], defaults to 100<br />Only used to assist in recommending certain parameter values when memory is sufficient. When memory is insufficient, priority is given to ensuring no over-provisioning, and this parameter will automatically degrade. |

> **Note**: 
>
>- The minimum available memory for the database is 1.5G, and the minimum available CPU core count is 1. If the allocated memory or CPU is too low, the recommended parameters will be calculated based on the minimum values without errors.
>- If multiple database instances are deployed on the same server, it is recommended to manually set os_memory_limit to proportionally allocate system memory to prevent memory shortages.

***Example***

```plsql
-- Generate recommended parameters using default settings, without writing to the configuration file.
BEGIN
    DBMS_PARAM.OPTIMIZE();
END;
/

-- Use TAC table type, allocate 80% memory, 100% CPU, generate recommended parameters, and write to configuration file.
EXEC DBMS_PARAM.OPTIMIZE(True, 'TAC', 80);

-- Use default HEAP table type, allocate 100% memory, 100% CPU, generate recommended parameters, without writing to the configuration file.
EXEC DBMS_PARAM.OPTIMIZE(NULL, NULL, 100, 100);

-- Use LSC table type, allocate 100% memory, 100% CPU, specify datafile and redofile paths, generate recommended parameters, without writing to the configuration file.
EXEC DBMS_PARAM.OPTIMIZE(NULL, 'LSC', NULL, NULL, '/home/yashan/data', '/home/yashan/redo');

-- Specify parameters by name, use LSC table type, allocate 100% memory, 100% CPU, based on TPCH SF100 scenario, generate recommended parameters, and write to configuration file.
EXEC DBMS_PARAM.OPTIMIZE(apply_parameter=>True, table_type=>'LSC', os_memory_limit=>100, os_cpu_limit=>100, scene=>'TPCH', scale_factor=>100)
```

## SHOW_RECOMMEND

```plsql
DBMS_PARAM.SHOW_RECOMMEND();
```

SHOW_RECOMMEND is a function that returns a report of the most recent recommended parameters.

***Example*** for data only for reference

```plsql
SELECT DBMS_PARAM.SHOW_RECOMMEND() FROM dual;

DBMS_PARAM.SHOW_RECOMMEND()
----------------------------------------------------------------
********** Recommended Settings For TAC Table ************
+--------------------------------+-------------+-------------+---------+
|            name                |  current    |  recommend  | restart |
+--------------------------------+-------------+-------------+---------+
| DATA_BUFFER_SIZE               |     11247M  |      11247M |  True   |
| VM_BUFFER_SIZE                 |      2396M  |       2396M |  True   |
| WORK_AREA_POOL_SIZE            |        64M  |         64M |  True   |
| SHARE_POOL_SIZE                |      2396M  |       2396M |  True   |
| MAX_PARALLEL_WORKERS           |         32  |          32 |  True   |
| SCOL_DATA_BUFFER_SIZE          |       128M  |        128M |  True   |
| SCOL_DATA_PRELOADERS           |          2  |           2 |  True   |
| COLUMNAR_VM_BUFFER_SIZE        |      7375M  |       7375M |  True   |
| COLUMNAR_BULK_SIZE             |       2048  |        2048 |  True   |
| PQ_POOL_SIZE                   |       128M  |        128M |  True   |
| MAX_SESSIONS                   |        500  |         500 |  True   |
| MAX_WORKERS                    |          0  |           0 |  True   |
| TAB_QUEUE_WINDOW_SIZE          |          4  |           4 |  True   |
| BLOOM_FILTER_FACTOR            |         .3  |          .3 |  True   |
| DEGREE_OF_PARALLEL             |          1  |           1 |  True   |
| RECOVERY_PARALLELISM           |          8  |           8 |  True   |
+--------------------------------+-------------+-------------+---------+
| other memory                   |                               1872M |
| total memory                   |                              25609M |
+--------------------------------+-------------+-------------+---------+
Note: You can execute 'DBMS_PARAM.APPLY_RECOMMEND()' to apply the recommend parameters.
      After applying the parameters, you need to restart the database.
```

## APPLY_RECOMMEND

```plsql
DBMS_PARAM.APPLY_RECOMMEND();
```

This stored procedure applies the recommended parameters and writes the most recent recommended parameters to the configuration parameter file. The database must be restarted after execution for the parameters to take effect.

***Example***
```plsql
EXEC DBMS_PARAM.APPLY_RECOMMEND();
```

## SHOW_MEMORY_LIMIT

```plsql
DBMS_PARAM.SHOW_MEMORY_LIMIT();
```

SHOW_MEMORY_LIMIT is a function that returns the high value of system memory usage under the current configuration.

> **Note**:
>
> The system memory usage high value obtained by executing the SHOW_MEMORY_LIMIT function may be larger than the available memory value passed during the execution of the OPTIMIZE stored procedure.

***Example*** for data only for reference

```plsql
SELECT DBMS_PARAM.SHOW_MEMORY_LIMIT() from dual;

DBMS_PARAM.SHOW_MEMO                                             
---------------------------------------------------------------- 
********** Default table type: LSC ************
+--------------------------------+----------+--------+----------+
|            name                | setting  |  num   |  memory  |
+--------------------------------+----------+--------+----------+
| DATA_BUFFER_SIZE               |     256M |      1 |     256M |
| VM_BUFFER_SIZE                 |      32M |      1 |      32M |
| WORK_AREA_POOL_SIZE            |      64M |      1 |      64M |
| SHARE_POOL_SIZE                |     256M |      1 |     256M |
| LARGE_POOL_SIZE                |      32M |      1 |      32M |
| SCOL_DATA_BUFFER_SIZE          |     128M |      1 |     128M |
| COLUMNAR_VM_BUFFER_SIZE        |     128M |      1 |     128M |
| PQ_POOL_SIZE                   |     128M |      1 |     128M |
| REDO_BUFFER_SIZE               |       8M |      2 |      16M |
| MAX_WORKERS                    |       16 |        |          |
| MAX_PARALLEL_WORKERS           |      128 |        |          |
| WORK_AREA_STACK_SIZE           |    1024K |    144 |     144M |
| private log buffer size        |     128K |    144 |      18M |
| MAX_SESSIONS                   |      128 |        |          |
| WORK_AREA_HEAP_SIZE            |       2M |    128 |     256M |
| reserved memory for db         |     200M |      1 |     200M |
+--------------------------------+----------+--------+----------+
| memory limit                   |          |        | 1.68164G |
+--------------------------------+----------+--------+----------+
```

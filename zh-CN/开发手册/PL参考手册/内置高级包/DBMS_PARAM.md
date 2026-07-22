DBMS_PARAM包提供了一组变量、函数和存储过程，用于设置数据库的推荐参数。推荐参数调整了一些关键参数的配置，会尽可能避免低级错误，但不保证解决所有配置项问题，对于具体场景还需要细化调优。

拥有ALTER SYSTEM权限的用户可以根据业务类型和系统资源状况，使用该高级包查看和应用推荐参数。

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

此存储过程用于生成推荐参数，旧的推荐参数将会被覆盖。

此存储过程的所有参数都有默认值，最少输入0个参数，最多输入8个参数。

> **Caution**:
>
> - 生成推荐参数的过程中，系统会测试datafile和redofile所在的磁盘性能，因此建议在没有业务的情况下执行，以免影响推荐参数的结果准确性。

|  参数| 描述|
| :--- | :---- |
| apply_parameter| 生成推荐参数后是否立刻写入配置文件，默认为FALSE。若设置为TRUE，参数会立即写入配置文件，但还需重启数据库相关参数才能生效 |
| table_type| 主要业务的表类型，可选[HEAP,TAC,LSC]，默认为HEAP |
| os_memory_limit| 服务器可供数据库使用内存百分比上限，默认为100，即分配所有内存，建议按需设置 |
| os_cpu_limit| 服务器可供数据库使用CPU百分比上限，默认为100，即分配所有CPU |
| data_path       | The path where the datafile is located, default is '', the system will automatically obtain the datafile path.<br>NOMOUNT mode default is $YASDB_DATA/dbfiles.<br>MOUNT and OPEN modes default to a random datafile path. |
| redo_path| redofile所在路径，默认为''，系统会自动获取redofile路径<br>NOMOUNT模式下，默认为$YASDB_DATA/dbfiles<br>MOUNT和OPEN模式下，默认为随机一个redofile所在路径 |
| scene| 参数调优的场景，默认为NORMAL<br>* 在单机部署、共享集群部署以及分布式集群部署中，可选[NORMAL,BATCH] <br />* 在存算一体分布式集群部署中，可选[NORMAL,TPCH] <br /><br />各个参数值的具体适用场景如下：<br />* NORMAL：通用混合负载场景，适用于所有表类型<br />* BATCH：批量处理场景，仅当表类型为HEAP时可设置为BATCH。当业务主要为OLTP业务使用了HEAP表时，如遇到周期性跑批场景性能不佳的情况，可考虑使用BATCH场景参数进行调优<br />* TPCH：复杂分析查询场景，仅当表类型为LSC时可设置为TPCH。使用TPCH场景进行调优推荐时，必须确保所有节点都执行参数推荐，否则可能重启时可能会因为连接参数不匹配而导致失败 |
| scale_factor| 诸如TPCH等测试场景的数据量（单位：GB），取值范围为[1, 100000]，默认为100<br />仅用于内存足够的情况下辅助部分参数值的推荐，内存不足时优先保证不超配，该参数会自动降级 |

> **Note**: 
>
>- 数据库最小可用内存为1.5G，最小可用CPU核数为1，如果分配的内存或CPU过小，将按最小值计算推荐参数，而不会报错。
>- 如果同一台服务器上部署多个数据库实例，建议手动设置os_memory_limit，将系统内存按比例划分，防止内存不足。

示例
```plsql
-- 使用默认参数生成推荐参数，不写入配置文件。
BEGIN
    DBMS_PARAM.OPTIMIZE();
END;
/

-- 使用TAC表类型，分配80%的内存，100%的CPU，生成推荐参数后，写入配置文件。
EXEC DBMS_PARAM.OPTIMIZE(True, 'TAC', 80);

-- 使用默认的HEAP表类型，分配100%的内存，100%的CPU，生成推荐参数后，不写入配置文件。
EXEC DBMS_PARAM.OPTIMIZE(NULL, NULL, 100, 100);

-- 使用LSC表类型，分配100%的内存，100%的CPU，指定datafile和redofile的路径，生成推荐参数后，不写入配置文件。
EXEC DBMS_PARAM.OPTIMIZE(NULL, 'LSC', NULL, NULL, '/home/yashan/data', '/home/yashan/redo');

-- 通过参数名指定，使用LSC表类型，分配100%的内存，100%的CPU，基于TPCH SF100场景，生成推荐参数后，写入配置文件。
EXEC DBMS_PARAM.OPTIMIZE(apply_parameter=>True, table_type=>'LSC', os_memory_limit=>100, os_cpu_limit=>100, scene=>'TPCH', scale_factor=>100)
```

## SHOW_RECOMMEND

```plsql
DBMS_PARAM.SHOW_RECOMMEND();
```

SHOW_RECOMMEND为一个函数，返回最近一次推荐参数的报告。

示例（数据仅供参考）

```plsql
SQL> SELECT DBMS_PARAM.SHOW_RECOMMEND() FROM dual;

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

此存储过程应用推荐参数，将最近一次的推荐参数写入配置参数文件。执行后必须重启数据库，以便让参数生效。

示例
```plsql
EXEC DBMS_PARAM.APPLY_RECOMMEND();
```

## SHOW_MEMORY_LIMIT

```plsql
DBMS_PARAM.SHOW_MEMORY_LIMIT();
```

SHOW_MEMORY_LIMIT为一个函数，返回当前配置项下，系统使用内存的上限值。

> **Note**:
>
> 执行SHOW_MEMORY_LIMIT函数得到的系统使用内存上限值可能比执行OPTIMIZE存储过程时传入的可用内存值大。

示例（数据仅供参考）

```plsql
SQL> SELECT DBMS_PARAM.SHOW_MEMORY_LIMIT() from dual;

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

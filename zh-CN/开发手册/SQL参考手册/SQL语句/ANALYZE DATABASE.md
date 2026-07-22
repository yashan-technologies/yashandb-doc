通用描述
----

ANALYZE DATABASE用于收集存算一体分布式集群数据库的统计信息，与之对应的是[DBMS_STATS](../../PL参考手册/内置高级包/DBMS_STATS)高级包的GATHER_DATABASE_STATS程序。

语句定义
----

**analyze database::=**

```ebnf
= ANALYZE DATABASE [(OPTIONS options|ESTIMATE_PERCENT estimate_value|BLOCK_SAMPLE block_value|PARALLEL_DEGREE parallel_value|METHOD_OPTION method_clause|GRANULARITY "'" graularity_value "'"|INDEX_CASCADE index_value|GATHER_SYS gather_sys_value) 
{" " (OPTIONS options|ESTIMATE_PERCENT estimate_value|BLOCK_SAMPLE block value|PARALLEL_DEGREE parallel_value|METHOD_OPTION method_clause|GRANULARITY "'" graularity_value "'"|INDEX_CASCADE index_value|GATHER_SYS gather_sys_value)}].
```

### OPTIONS

数据库统计信息收集选项，可省略，则为GATHER AUTO，等同于指定[DBMS_STATS](../../PL参考手册/内置高级包/DBMS_STATS)中高级包GATHER_DATABASE_STATS的参数options。

### ESTIMATE_PERCENT

指定统计的采样率，可省略，等同于指定[DBMS_STATS](../../PL参考手册/内置高级包/DBMS_STATS)中高级包GATHER_DATABASE_STATS的参数estimate_percent。

### BLOCK_SAMPLE

指定是否采用块级采样，默认为FALSE即行采样，可省略，等同于指定[DBMS_STATS](../../PL参考手册/内置高级包/DBMS_STATS)中高级包GATHER_DATABASE_STATS的参数block_sample。

### PARALLEL_DEGREE

指定并行度，对于大表，增大并行度可以提升统计信息收集的效率，可省略，则parallel_value默认为1，等同于指定[DBMS_STATS](../../PL参考手册/内置高级包/DBMS_STATS)中高级包GATHER_DATABASE_STATS的参数degree。

### METHOD_OPTION

指定列统计信息选项，可省略，等同于指定[DBMS_STATS](../../PL参考手册/内置高级包/DBMS_STATS)中高级包GATHER_DATABASE_STATS的参数method_option。

### GRANULARITY

指定分区统计粒度，可省略，等同于指定[DBMS_STATS](../../PL参考手册/内置高级包/DBMS_STATS)中高级包GATHER_DATABASE_STATS的参数granularity。

### INDEX_CASCADE

指定是否收集索引统计信息（true/false），可省略，则index_value默认为false，等同于指定[DBMS_STATS](../../PL参考手册/内置高级包/DBMS_STATS)中高级包GATHER_DATABASE_STATS的参数cascade。

### GATHER_SYS

指定是否收集系统表的统计信息（true/false），可省略，则默认为false，等同于指定[DBMS_STATS](../../PL参考手册/内置高级包/DBMS_STATS)中高级包GATHER_DATABASE_STATS的参数gather_sys。

示例（存算一体分布式集群部署）

```sql
ANALYZE DATABASE OPTIONS 'GATHER AUTO' ESTIMATE_PERCENT 1 PARALLEL_DEGREE 2 METHOD_OPTION 'FOR ALL COLUMNS SIZE AUTO' GRANULARITY 'AUTO' INDEX_CASCADE TRUE GATHER_SYS FALSE;
```

通用描述
----

ANALYZE SCHEMA用于收集指定用户下所有对象（表、AC、列、索引）的统计信息，与之对应的是[DBMS_STATS](../../PL参考手册/内置高级包/DBMS_STATS)高级包的GATHER_SCHEMA_STATS程序。

语句定义
----

**analyze schema::=**

```ebnf+diagram
syntax::= ANALYZE SCHEMA OWNER [(ESTIMATE_PERCENT estimate_value|BLOCK_SAMPLE block_value|METHOD_OPTION method_clause|PARALLEL_DEGREE parallel_value|GRANULARITY "'" graularity_value "'"|INDEX_CASCADE index_value) 
{" " (ESTIMATE_PERCENT estimate_value|BLOCK_SAMPLE block_value|METHOD_OPTION method_clause|PARALLEL_DEGREE parallel_value|GRANULARITY "'" graularity_value "'"|INDEX_CASCADE index_value)}]
```

### 1. ESTIMATE\_PERCENT

指定统计的采样率，可省略，等同于指定[DBMS_STATS](../../PL参考手册/内置高级包/DBMS_STATS)中高级包GATHER_SCHEMA_STATS的参数estimate_percent。

### 2. BLOCK\_SAMPLE

指定是否采用块级采样，默认为FALSE即行采样，可省略，等同于指定[DBMS_STATS](../../PL参考手册/内置高级包/DBMS_STATS)中高级包GATHER_SCHEMA_STATS的参数block_sample。

### 3. METHOD\_OPTION

指定列统计信息选项，可省略，等同于指定[DBMS_STATS](../../PL参考手册/内置高级包/DBMS_STATS)中高级包GATHER_SCHEMA_STATS的参数method_option。

### 4. PARALLEL\_DEGREE

指定并行度，对于大表，增大并行度可以提升统计信息收集的效率，可省略，则parallel_value默认为1，等同于指定[DBMS_STATS](../../PL参考手册/内置高级包/DBMS_STATS)中高级包GATHER_SCHEMA_STATS的参数degree。

### 5. GRANULARITY

指定分区统计粒度，可省略，等同于指定[DBMS_STATS](../../PL参考手册/内置高级包/DBMS_STATS)中高级包GATHER_SCHEMA_STATS的参数granularity。

### 6. INDEX\_CASCADE

指定是否收集索引统计信息（true/false），可省略，则index_value默认为false，等同于指定[DBMS_STATS](../../PL参考手册/内置高级包/DBMS_STATS)中高级包GATHER_SCHEMA_STATS的参数cascade。

示例（存算一体分布式集群部署）

```sql
ANALYZE SCHEMA SALES ESTIMATE_PERCENT 1 BLOCK_SAMPLE TRUE METHOD_OPTION 'FOR ALL COLUMNS SIZE AUTO' PARALLEL_DEGREE 1 GRANULARITY 'ALL' INDEX_CASCADE TRUE;
```

通用描述
----

ANALYZE TABLE用于收集存算一体分布式集群部署中的表、AC或分区的统计信息，与之对应的是[DBMS_STATS](../内置高级包/DBMS_STATS)高级包的GATHER_TABLE_STATS程序。

对于组合分区表，不可指定收集某个分区/子分区，仅收集其GLOBAL级统计信息。

语句定义
----

**analyze table::=**

```ebnf+diagram
syntax::= ANALYZE TABLE table_name [(PARTITION partition_value|ESTIMATE_PERCENT estimate_value|BLOCK_SAMPLE block_value|METHOD_OPTION method_clause|PARALLEL_DEGREE parallel_value|GRANULARITY "'" graularity_value "'"|INDEX_CASCADE index_value) 
{" " (PARTITION partition_value|ESTIMATE_PERCENT estimate_value|BLOCK_SAMPLE block_value|METHOD_OPTION method_clause|PARALLEL_DEGREE parallel_value|GRANULARITY "'" graularity_value "'"|INDEX_CASCADE index_value)}]
```

**method_clause::=**

```ebnf+diagram
syntax::= "'" (FOR ALL COLUMNS [size_clause]
| FOR COLUMNS "(" (column_name) {"," (column_name)} ")" [size_clause]
) "'"
```

**size_clause::=**

```ebnf+diagram
syntax::=  SIZE (integer|AUTO)
```

### 1. PARTITION

按指定的分区名收集统计信息，可省略，则partition_value默认为NULL，表示不按分区，收集整张表信息，等同于在[DBMS_STATS](../内置高级包/DBMS_STATS)中高级包GATHER_TABLE_STATS的参数partname。

### 2. ESTIMATE\_PERCENT

指定统计的采样率，可省略，等同于指定[DBMS_STATS](../内置高级包/DBMS_STATS)中高级包GATHER_TABLE_STATS的参数estimate_percent。

### 3. BLOCK\_SAMPLE

指定是否采用块级采样，默认为FALSE即行采样，可省略，等同于指定[DBMS_STATS](../内置高级包/DBMS_STATS)中高级包GATHER_TABLE_STATS的参数block_sample。

### 4. METHOD\_OPTION

指定列统计信息选项，可省略，等同于指定[DBMS_STATS](../内置高级包/DBMS_STATS)中高级包GATHER_TABLE_STATS的参数method_option。

#### 4.1. size\_clause

指定直方图信息，可省略，则默认为SIZE AUTO。

- integer：直方图的bucket数量，范围为[1,2048]。
- AUTO： 由系统决定是否生成直方图。

### 5. PARALLEL\_DEGREE

指定并行度，对于大表，增大并行度可以提升统计信息收集的效率，可省略，则parallel_value默认为1，等同于指定[DBMS_STATS](../内置高级包/DBMS_STATS)中高级包GATHER_TABLE_STATS的参数degree。

### 6. GRANULARITY

指定分区统计粒度，可省略，等同于指定[DBMS_STATS](../内置高级包/DBMS_STATS)中高级包GATHER_TABLE_STATS的参数granularity。

### 7. INDEX\_CASCADE

指定是否收集索引统计信息（true/false），可省略，则index_value默认为false，等同于指定[DBMS_STATS](../内置高级包/DBMS_STATS)中高级包GATHER_TABLE_STATS的参数cascade。

示例（存算一体分布式集群部署）

```sql
ANALYZE TABLE branches PARTITION NULL ESTIMATE_PERCENT 0.5 BLOCK_SAMPLE true 
INDEX_CASCADE true METHOD_OPTION 'FOR COLUMNS (branch_no, area_no) SIZE AUTO' 
GRANULARITY 'AUTO' PARALLEL_DEGREE 1;
```

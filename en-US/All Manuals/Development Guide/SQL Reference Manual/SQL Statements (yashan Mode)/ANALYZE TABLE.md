General Description
----

ANALYZE TABLE is used to collect statistics for tables, AC, or partitions in ISC Distributed Cluster Deployment, corresponding to the GATHER_TABLE_STATS procedure of the [DBMS_STATS](../../PL Reference Manual/Built-in Advanced PL Packages/DBMS_STATS) advanced package.

For composite partition tables, it is not possible to specify collection for a specific partition/subpartition; only GLOBAL level statistics can be collected.

Statement Definition
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

Collect statistics by the specified partition name. It can be omitted, and partition_value defaults to NULL, which indicates that the entire table information is collected, equivalent to the partname parameter in the GATHER_TABLE_STATS of [DBMS_STATS](../../PL Reference Manual/Built-in Advanced PL Packages/DBMS_STATS) advanced package.

### 2. ESTIMATE\_PERCENT

Designates the sampling rate for statistics. It can be omitted and is equivalent to specifying the estimate_percent parameter in the GATHER_TABLE_STATS of [DBMS_STATS](../../PL Reference Manual/Built-in Advanced PL Packages/DBMS_STATS) advanced package.

### 3. BLOCK\_SAMPLE

Specifies whether block-level sampling is used. The default is FALSE (i.e., row sampling) and can be omitted, equivalent to specifying the block_sample parameter in the GATHER_TABLE_STATS of [DBMS_STATS](../../PL Reference Manual/Built-in Advanced PL Packages/DBMS_STATS) advanced package.

### 4. METHOD\_OPTION

Specifies the column statistics options. It can be omitted and is equivalent to specifying the method_option parameter in the GATHER_TABLE_STATS of [DBMS_STATS](../../PL Reference Manual/Built-in Advanced PL Packages/DBMS_STATS) advanced package.

#### 4.1. size\_clause

Specifies histogram information. It can be omitted, and the default value is SIZE AUTO.

- integer: The number of buckets for the histogram, ranging from [1,2048].
- AUTO: The system determines whether to generate a histogram.

### 5. PARALLEL\_DEGREE

Specifies the degree of parallelism. Increasing the degree can enhance the efficiency of statistics collection for large tables. It can be omitted, and the parallel_value defaults to 1, which is equivalent to the degree parameter in the GATHER_TABLE_STATS of [DBMS_STATS](../../PL Reference Manual/Built-in Advanced PL Packages/DBMS_STATS) advanced package.

### 6. GRANULARITY

Specifies the granularity of partition statistics. It can be omitted and is equivalent to the granularity parameter in the GATHER_TABLE_STATS of [DBMS_STATS](../../PL Reference Manual/Built-in Advanced PL Packages/DBMS_STATS) advanced package.

### 7. INDEX\_CASCADE

Specifies whether to collect index statistics (true/false). It can be omitted, and index_value defaults to false, which is equivalent to the cascade parameter in the GATHER_TABLE_STATS of [DBMS_STATS](../../PL Reference Manual/Built-in Advanced PL Packages/DBMS_STATS) advanced package.

***Example*** for ISC Distributed Cluster Deployment

```sql
ANALYZE TABLE branches PARTITION NULL ESTIMATE_PERCENT 0.5 BLOCK_SAMPLE true 
INDEX_CASCADE true METHOD_OPTION 'FOR COLUMNS (branch_no, area_no) SIZE AUTO' 
GRANULARITY 'AUTO' PARALLEL_DEGREE 1;
```

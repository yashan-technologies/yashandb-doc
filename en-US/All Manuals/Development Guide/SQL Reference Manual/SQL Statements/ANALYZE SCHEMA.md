General Description
----

`ANALYZE SCHEMA` is used to collect statistics for all objects (tables, ACs, columns, indexes) under the specified user in ISC Distributed Cluster Deployment. This corresponds to the `GATHER_SCHEMA_STATS` procedure in the advanced package [DBMS_STATS](../../PL Reference Manual/Built-in Advanced PL Packages/DBMS_STATS).

Statement Definition
----

**analyze schema::=**

```ebnf+diagram
syntax::= ANALYZE SCHEMA OWNER [(ESTIMATE_PERCENT estimate_value|BLOCK_SAMPLE block_value|METHOD_OPTION method_clause|PARALLEL_DEGREE parallel_value|GRANULARITY "'" graularity_value "'"|INDEX_CASCADE index_value) 
{" " (ESTIMATE_PERCENT estimate_value|BLOCK_SAMPLE block_value|METHOD_OPTION method_clause|PARALLEL_DEGREE parallel_value|GRANULARITY "'" graularity_value "'"|INDEX_CASCADE index_value)}]
```

### 1. ESTIMATE\_PERCENT

Specifies the sampling rate for statistics, which is optional and is equivalent to specifying the `estimate_percent` parameter in the `GATHER_SCHEMA_STATS` procedure of the advanced package [DBMS_STATS](../../PL Reference Manual/Built-in Advanced PL Packages/DBMS_STATS).

### 2. BLOCK\_SAMPLE

Specifies whether block-level sampling is used. The default is FALSE, which means row sampling is used. This is optional and is equivalent to specifying the `block_sample` parameter in the `GATHER_SCHEMA_STATS` procedure of the advanced package [DBMS_STATS](../../PL Reference Manual/Built-in Advanced PL Packages/DBMS_STATS).

### 3. METHOD\_OPTION

Specifies the column statistics option. This is optional and is equivalent to specifying the `method_option` parameter in the `GATHER_SCHEMA_STATS` procedure of the advanced package [DBMS_STATS](../../PL Reference Manual/Built-in Advanced PL Packages/DBMS_STATS).

### 4. PARALLEL\_DEGREE

Specifies the degree of parallelism. Increasing parallelism can improve the efficiency of statistics collection for large tables. This is optional, and the default for `parallel_value` is 1, which is equivalent to specifying the `degree` parameter in the `GATHER_SCHEMA_STATS` procedure of the advanced package [DBMS_STATS](../../PL Reference Manual/Built-in Advanced PL Packages/DBMS_STATS).

### 5. GRANULARITY

Specifies the statistics granularity for partitions. This is optional and is equivalent to specifying the `granularity` parameter in the `GATHER_SCHEMA_STATS` procedure of the advanced package [DBMS_STATS](../../PL Reference Manual/Built-in Advanced PL Packages/DBMS_STATS).

### 6. INDEX\_CASCADE

Specifies whether to collect index statistics (true/false). This is optional, and the default for `index_value` is false, which is equivalent to specifying the `cascade` parameter in the `GATHER_SCHEMA_STATS` procedure of the advanced package [DBMS_STATS](../../PL Reference Manual/Built-in Advanced PL Packages/DBMS_STATS).

***Example*** for ISC Distributed Cluster Deployment

```sql
ANALYZE SCHEMA SALES ESTIMATE_PERCENT 1 BLOCK_SAMPLE TRUE METHOD_OPTION 'FOR ALL COLUMNS SIZE AUTO' PARALLEL_DEGREE 1 GRANULARITY 'ALL' INDEX_CASCADE TRUE;
```

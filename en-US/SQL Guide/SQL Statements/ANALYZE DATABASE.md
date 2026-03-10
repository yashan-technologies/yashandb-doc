General Description
----

ANALYZE DATABASE is used to collect the statistics of the database in ISC Distributed Cluster Deployment, corresponding to the GATHER_DATABASE_STATS procedure in the [DBMS_STATS](../../All Manuals/Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_STATS) advanced package.

Statement Definition
----

**analyze database::=**

```ebnf+diagram
syntax::= ANALYZE DATABASE [(OPTIONS options|ESTIMATE_PERCENT estimate_value|BLOCK_SAMPLE block_value|PARALLEL_DEGREE parallel_value|METHOD_OPTION method_clause|GRANULARITY "'" graularity_value "'"|INDEX_CASCADE index_value|GATHER_SYS gather_sys_value) 
{" " (OPTIONS options|ESTIMATE_PERCENT estimate_value|BLOCK_SAMPLE block value|PARALLEL_DEGREE parallel_value|METHOD_OPTION method_clause|GRANULARITY "'" graularity_value "'"|INDEX_CASCADE index_value|GATHER_SYS gather_sys_value)}]
```

### 1. OPTIONS

Database statistics collection options. This can be omitted, which defaults to GATHER AUTO, equivalent to specifying the options parameter in the GATHER_DATABASE_STATS of the [DBMS_STATS](../../All Manuals/Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_STATS) advanced package.

### 2. ESTIMATE\_PERCENT

Specifies the sampling rate of statistics. This can be omitted, which is equivalent to specifying the estimate_percent parameter in the GATHER_DATABASE_STATS of the [DBMS_STATS](../../All Manuals/Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_STATS) advanced package.

### 3. BLOCK\_SAMPLE

Specifies whether to use block-level sampling, defaulting to FALSE for row sampling. This can be omitted, which is equivalent to specifying the block_sample parameter in the GATHER_DATABASE_STATS of the [DBMS_STATS](../../All Manuals/Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_STATS) advanced package.

### 4. PARALLEL\_DEGREE

Specifies the degree of parallelism. For large tables, increasing the degree can enhance the efficiency of statistics collection. This can be omitted, with the default parallel_value being 1, equivalent to specifying the degree parameter in the GATHER_DATABASE_STATS of the [DBMS_STATS](../../All Manuals/Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_STATS) advanced package.

### 5. METHOD\_OPTION

Specifies column statistics options. This can be omitted, which is equivalent to specifying the method_option parameter in the GATHER_DATABASE_STATS of the [DBMS_STATS](../../All Manuals/Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_STATS) advanced package.

### 6. GRANULARITY

Specifies the statistics granularity for partitions. This can be omitted, which is equivalent to specifying the granularity parameter in the GATHER_DATABASE_STATS of the [DBMS_STATS](../../All Manuals/Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_STATS) advanced package.

### 7. INDEX\_CASCADE

Specifies whether to collect index statistics (true/false). This can be omitted, with the default index_value being false, equivalent to specifying the cascade parameter in the GATHER_DATABASE_STATS of the [DBMS_STATS](../../All Manuals/Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_STATS) advanced package.

### 8. GATHER\_SYS

Specifies whether to collect statistics for system tables (true/false). This can be omitted, with the default being false, equivalent to specifying the gather_sys parameter in the GATHER_DATABASE_STATS of the [DBMS_STATS](../../All Manuals/Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_STATS) advanced package.

***Example*** for ISC Distributed Cluster Deployment

```sql
ANALYZE DATABASE OPTIONS 'GATHER AUTO' ESTIMATE_PERCENT 1 PARALLEL_DEGREE 2 METHOD_OPTION 'FOR ALL COLUMNS SIZE AUTO' GRANULARITY 'AUTO' INDEX_CASCADE TRUE GATHER_SYS FALSE;
```

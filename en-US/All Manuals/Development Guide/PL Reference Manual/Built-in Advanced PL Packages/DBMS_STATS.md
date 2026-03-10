The DBMS_STATS package provides a set of variables, functions, and procedures for managing the optimizer's statistics.

In addition to collecting table/schema/database statistics and locking/unlocking partition/table/schema statistics, other functions/procedures are not applicable to ISC Distributed Cluster Deployment.

## Manually Setting Statistics

### SET\_TABLE\_STATS

```plsql
DBMS_STATS.SET_TABLE_STATS (
	ownname    VARCHAR,
	tabname    VARCHAR,
	partname   VARCHAR,
	numrows    NUMBER,
	numblks    NUMBER,
	avgrlen    NUMBER
);
```

This procedure is used to manually set the statistics for a table or partition, replacing the old statistics, and is mainly used in testing or statistics correction scenarios.

This procedure is not applicable to composite partition tables.

|Parameter |Description |
| :--- | :---- |
| ownname| Username |
| tabname| Table name |
| partname| Partition name|
| numrows| Number of rows in the table or partition |
| numblks| Number of data blocks in the table or partition |
| avgrlen| Average row length of the table or partition |

***Example*** for Standalone/YAC/Distributed Cluster Deployment
```plsql
BEGIN
    DBMS_STATS.SET_TABLE_STATS( 'SALES', 'SALES_INFO_RANGE', 'P_SALES_INFO_RANGE_1', 10, 1, 15);
END;
/
```

### SET\_INDEX\_STATS

```plsql
DBMS_STATS.SET_INDEX_STATS (
    ownname      VARCHAR,
    indname      VARCHAR,
    partname     VARCHAR,
    numrows      NUMBER,
    numlblks     NUMBER,
    numdist      NUMBER,
    numfirstdist NUMBER,
    avglblk      NUMBER,
    avgdblk      NUMBER,
    clstfct      NUMBER,
    indlevel     NUMBER,
    dist2keys    NUMBER,
    dist3keys    NUMBER,
    dist4keys    NUMBER
);
```

This procedure is used to manually set the statistics for an index or partition, replacing the old statistics, and is mainly used in testing or statistics correction scenarios.

This procedure is not applicable to index information of composite partition tables.

|Parameter |Description |
| :----------- | :----------------------------- |
| ownname      | Username                         |
| indname      | Index name                         |
| partname     | Index partition name                     |
| numrows      | Number of distinct keys in the index |
| numlblks     | Number of leaf blocks                     |
| numdist      | Number of distinct values for the index keys               |
| numfirstdist | Number of distinct values for the first column of the composite index   |
| avglblk      | Average number of keys per leaf block     |
| avgdblk      | Average number of data blocks pointed to by each leaf block |
| clstfct      | Clustering factor of the index                 |
| indlevel     | Height of the BTree                    |
| dist2keys    | Number of distinct values for the first two columns of the composite index   |
| dist3keys    | Number of distinct values for the first three columns of the composite index   |
| dist4keys    | Number of distinct values for the first four columns of the composite index   |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
BEGIN
    DBMS_STATS.SET_INDEX_STATS('SALES', 'IDX_FINANCE_INFO_1', null, 100, 10, 10, 1, 10, 10, 1, 1, 2, 10, 10);
END;
/
```

### SET\_COLUMN\_STATS 

```plsql
DBMS_STATS.SET_COLUMN_STATS (
    ownname    VARCHAR,
    tabname    VARCHAR,
    colname    VARCHAR
    partname   VARCHAR,
    distcnt    NUMBER,
    density    NUMBER,
    nullcnt    NUMBER,
    avgclen    NUMBER,
);
```

This procedure is used to manually set the column statistics for a table or partition, replacing the old statistics, and is mainly used in testing or statistics correction scenarios.

This procedure is not applicable to composite partition tables.

|Parameter |Description |
| :--- | :---- |
| ownname| Username |
| tabname| Table name |
| colname| Column name |
| partname| Partition name |
| distcnt | Number of distinct values in the column |
| density| Density of the column, range [0,1] |
| nullcnt| Number of NULL values in the column |
| avgclen| Average column length |

***Example*** for Standalone/YAC/Distributed Cluster Deployment
```plsql
BEGIN
  DBMS_STATS.SET_COLUMN_STATS( 'SALES', 'SALES_INFO_RANGE', 'AMOUNT', 'P_SALES_INFO_RANGE_1', 10, 0.2, 15, 10);
END;
/
```

## Deleting Statistics

### DELETE\_TABLE\_STATS 

```plsql
DBMS_STATS.DELETE_TABLE_STATS (
    ownname           VARCHAR,
    tabname           VARCHAR,
    partname          VARCHAR DEFAULT NULL,
    cascade_parts     BOOLEAN DEFAULT TRUE,
    cascade_columns   BOOLEAN DEFAULT TRUE,
    cascade_indexes   BOOLEAN DEFAULT TRUE,
    force             BOOLEAN DEFAULT FALSE
);
```

This procedure is used to manually delete table statistics, mainly in the context of cleaning inaccurate statistics.

|Parameter |Description |
| :--- | :---- |
| ownname| Username |
| tabname| Table name |
| partname| Partition name |
| cascade_parts| Whether to delete statistics associated with partitions of the table |
| cascade_columns| Whether to delete statistics associated with columns of the table |
| cascade_indexes| Whether to delete statistics associated with indexes of the table |
| force| Whether to forcefully delete statistics that are locked |

***Example*** for Standalone/YAC/Distributed Cluster Deployment
```plsql
BEGIN
    DBMS_STATS.DELETE_TABLE_STATS('SALES', 'SALES_INFO_RANGE', 'P_SALES_INFO_RANGE_1', true, true, true, false);
END;
/
```

### DELETE\_INDEX\_STATS 

```plsql
DBMS_STATS.DELETE_INDEX_STATS (
    ownname           VARCHAR,
    indname           VARCHAR,
    partname          VARCHAR DEFAULT NULL,
    cascade_parts     BOOLEAN DEFAULT TRUE,
    force             BOOLEAN DEFAULT FALSE
);
```

This procedure is used to manually delete index statistics, mainly in the context of cleaning inaccurate statistics.

This procedure is not applicable to index information of composite partition tables.

|Parameter |Description |
| :--- | :---- |
| ownname| Username |
| indname| Index name |
| partname| Index partition name |
| cascade_parts| Whether to delete statistics associated with partitions of the index |
| force| Whether to forcefully delete statistics that are locked |

***Example*** for Standalone/YAC/Distributed Cluster Deployment
```plsql
BEGIN
    DBMS_STATS.DELETE_INDEX_STATS('SALES', 'IDX_FINANCE_INFO_1', null, true, false);
END;
/
```

### DELETE\_SCHEMA\_STATS 

```plsql
DBMS_STATS.DELETE_SCHEMA_STATS (
    ownname           VARCHAR,
    force             BOOLEAN DEFAULT FALSE
);
```

This procedure is used to manually delete all object (tables, indexes, columns) statistics under a certain schema, mainly in the context of cleaning inaccurate statistics.

|Parameter |Description |
| :--- | :---- |
| ownname| Username |
| force| Whether to forcefully delete statistics that are locked |

***Example*** for Standalone/YAC/Distributed Cluster Deployment
```plsql
BEGIN
    DBMS_STATS.DELETE_SCHEMA_STATS('SALES', false);
END;
/
```

### DELETE\_COLUMN\_STATS 

```plsql
DBMS_STATS.DELETE_COLUMN_STATS (
    ownname         VARCHAR,
    tabname         VARCHAR,
    colname         VARCHAR,
    partname        VARCHAR,
    type            VARCHAR,
    cascade_parts   BOOLEAN DEFAULT TRUE,
    force           BOOLEAN DEFAULT FALSE
);
```

This procedure is used to manually delete column statistics, mainly in the context of cleaning inaccurate statistics.

|Parameter |Description |
| :--- | :---- |
| ownname| Username |
| tabname| Table name |
| colname| Column name |
| partname| Partition name |
| type| Deletion type:<br/>'ALL': deletes both column statistics and histogram<br/>'HISTOGRAM': deletes only the histogram |
| cascade_parts| Whether to delete statistics associated with partitions of the column |
| force| Whether to forcefully delete statistics that are locked |

***Example*** for Standalone/YAC/Distributed Cluster Deployment
```plsql
BEGIN
    DBMS_STATS.DELETE_COLUMN_STATS('SALES', 'SALES_INFO_RANGE', 'AMOUNT', 'P_SALES_INFO_RANGE_1', 'ALL', true, false);
END;
/
```

## Collecting Statistics

<span id="gathertablestats" name="gathertablestats" class="yaslink"></span>

### GATHER\_TABLE\_STATS

```plsql
DBMS_STATS.GATHER_TABLE_STATS (
    ownname             VARCHAR,
    tabname             VARCHAR,
    partname            VARCHAR DEFAULT NULL,
    estimate_percent    NUMBER  DEFAULT GET_PREFS('ESTIMATE_PERCENT', ownname, tabname),
    block_sample        BOOLEAN DEFAULT FALSE,
    method_opt          VARCHAR DEFAULT GET_PREFS('METHOD_OPT', ownname, tabname),
    degree              NUMBER  DEFAULT GET_PREFS('DEGREE', ownname, tabname),
    granularity         VARCHAR DEFAULT GET_PREFS('GRANULARITY', ownname, tabname),
    cascade             BOOLEAN DEFAULT GET_PREFS('CASCADE', ownname, tabname),
    no_invalidate       BOOLEAN DEFAULT GET_PREFS('NO_INVALIDATE', ownname, tabname)
);
```

This procedure is used to collect statistics for a table or partition.

For composite partition tables, it is not possible to specify collection for a particular partition/subpartition; it only collects GLOBAL-level statistics.

|Parameter |Description |
| :--- | :---- |
| ownname| Username |
| tabname| Table name |
| partname| Partition name |
| estimate_percent| Sampling rate, please refer to [statistics options](#statoption) |
| block_sample| Whether to use block-level sampling |
| method_opt| Column statistics options, please refer to [statistics options](#statoption) |
| degree| Degree of parallelism, increasing parallelism for large tables can improve statistics collection efficiency. Please refer to [statistics options](#statoption) |
| granularity| Partition statistics granularity, please refer to [statistics options](#statoption) |
| cascade | Whether to collect index statistics, please refer to [statistics options](#statoption) |
|no_invalidate | Whether to update execution plans in database cache after gathering Statistics. Reserved field, only for syntax compatibility.<br> TRUE: Do not update execution plans.<br> FALSE: Update execution plans.<br> DBMS_STATS.AUTO_INVALIDATE: The database decides on its own when to update cached execution plans.|

> **Note**
>
> Partition statistics can be collected but are not used by the optimizer.

***Example***

```plsql
BEGIN
    DBMS_STATS.GATHER_TABLE_STATS('SALES', 'SALES_INFO_RANGE', estimate_percent=>0.5, degree=>8);
    DBMS_STATS.GATHER_TABLE_STATS('SALES', 'SALES_INFO_RANGE', partname=>'P_SALES_INFO_RANGE_1', estimate_percent=>0.2, block_sample=>FALSE, method_opt=>'FOR ALL COLUMNS SIZE AUTO', degree=>4, granularity=>'AUTO', CASCADE=>TRUE);
END;
/
```

### GATHER\_INDEX\_STATS

```plsql
-- In GET_PREFS, tabname is the name of the table corresponding to the index
DBMS_STATS.GATHER_INDEX_STATS (
    ownname             VARCHAR,
    indname             VARCHAR,
    partname            VARCHAR DEFAULT NULL,
    estimate_percent    NUMBER  DEFAULT GET_PREFS('ESTIMATE_PERCENT', ownname, tabname),
    degree              NUMBER  DEFAULT GET_PREFS('DEGREE', ownname, tabname),
    granularity         VARCHAR DEFAULT GET_PREFS('GRANULARITY', ownname, tabname),
    no_invalidate       BOOLEAN DEFAULT GET_PREFS('NO_INVALIDATE', ownname, tabname)
);
```

This procedure is used to collect statistics for an index or index partition.

For composite partition indexes, it is not possible to specify collection for a particular partition/subpartition; it only collects GLOBAL-level statistics.

|Parameter |Description |
| :--- | :---- |
| ownname| Username |
| indname| Index name |
| partname| Index partition name, NULL indicates statistics for all partitions |
| estimate_percent| Sampling rate percentage, please refer to [statistics options](#statoption) |
| degree| Degree of parallelism, please refer to [statistics options](#statoption) |
| granularity| Partition statistics granularity, please refer to [statistics options](#statoption) |
|no_invalidate | Whether to update execution plans in database cache after gathering Statistics. Reserved field, only for syntax compatibility.<br> TRUE: Do not update execution plans.<br> FALSE: Update execution plans.<br> DBMS_STATS.AUTO_INVALIDATE: The database decides on its own when to update cached execution plans.|

***Example*** for Standalone/YAC/Distributed Cluster Deployment
```plsql
BEGIN
    DBMS_STATS.GATHER_INDEX_STATS('SALES', 'IDX_FINANCE_INFO_1', '', estimate_percent=>0.2, degree=>2, granularity=>'ALL');
END;
/
```

<span id="gatherschemastats" name="gatherschemastats" class="yaslink"></span>

### GATHER\_SCHEMA\_STATS

```plsql
DBMS_STATS.GATHER_SCHEMA_STATS (
    ownname           VARCHAR,
    estimate_percent  NUMBER  DEFAULT GET_PREFS('ESTIMATE_PERCENT'),
    block_sample      BOOLEAN DEFAULT FALSE,
    method_opt        VARCHAR DEFAULT GET_PREFS('METHOD_OPT'),
    degree            NUMBER  DEFAULT GET_PREFS('DEGREE'),
    granularity       VARCHAR DEFAULT GET_PREFS('GRANULARITY'),
    cascade           BOOLEAN DEFAULT GET_PREFS('CASCADE'),
    options           VARCHAR DEFAULT GET_PREFS('OPTIONS'),
    no_invalidate       BOOLEAN DEFAULT GET_PREFS('NO_INVALIDATE', ownname, tabname)
);
```

This procedure is used to collect statistics for all objects (tables, indexes, columns) under a specified user.

|Parameter |Description |
| :--------------- | :------------------------------------------------------ |
| ownname| Username |
| estimate_percent| Sampling rate percentage, please refer to [statistics options](#statoption) |
| block_sample| Whether to use block-level sampling |
| method_opt| Column statistics options, please refer to [statistics options](#statoption) |
| degree| Degree of parallelism, please refer to [statistics options](#statoption) |
| granularity| Partition statistics granularity, please refer to [statistics options](#statoption) |
| cascade | Whether to collect index statistics, please refer to [statistics options](#statoption) |
| options | Please refer to [statistics options](#statoption) |
|no_invalidate | Whether to update execution plans in database cache after gathering Statistics. Reserved field, only for syntax compatibility.<br> TRUE: Do not update execution plans.<br> FALSE: Update execution plans.<br> DBMS_STATS.AUTO_INVALIDATE: The database decides on its own when to update cached execution plans.|

```plsql
exec DBMS_STATS.GATHER_SCHEMA_STATS('SALES', estimate_percent=>1, degree=>8);
exec DBMS_STATS.GATHER_SCHEMA_STATS('SALES', estimate_percent=>1, block_sample=>TRUE, method_opt=>'FOR ALL COLUMNS SIZE AUTO', degree=>1, granularity=>'ALL', cascade=>TRUE, options=>'GATHER AUTO');
```

<span id="gatherdatabasestats" name="gatherdatabasestats" class="yaslink"></span>

### GATHER\_DATABASE\_STATS

```plsql
DBMS_STATS.GATHER_DATABASE_STATS (
    options             VARCHAR DEFAULT GET_PREFS('OPTIONS'),
    estimate_percent    NUMBER  DEFAULT GET_PREFS('ESTIMATE_PERCENT'),
    degree              NUMBER  DEFAULT GET_PREFS('DEGREE'),
    method_opt          VARCHAR DEFAULT GET_PREFS('METHOD_OPT'),
    granularity         VARCHAR DEFAULT GET_PREFS('GRANULARITY'),
    cascade             BOOLEAN DEFAULT GET_PREFS('CASCADE'),
    gather_sys          BOOLEAN DEFAULT FALSE,
    block_sample        BOOLEAN DEFAULT FALSE,
    no_invalidate       BOOLEAN DEFAULT GET_PREFS('NO_INVALIDATE', ownname, tabname)
);
```

This procedure is used to collect statistics for the database.

|Parameter |Description |
| :--------------- | :------------------------------------------------------ |
| options| Please refer to [statistics options](#statoption) |
| estimate_percent| Sampling rate, please refer to [statistics options](#statoption) |
| degree| Degree of parallelism, please refer to [statistics options](#statoption) |
| method_opt| Column statistics options, please refer to [statistics options](#statoption) |
| granularity| Partition statistics granularity, please refer to [statistics options](#statoption) |
| cascade| Whether to collect index statistics, please refer to [statistics options](#statoption) |
| gather_sys| Whether to collect statistics for system tables |
| block_sample| Whether to use block-level sampling |
|no_invalidate | Whether to update execution plans in database cache after gathering Statistics. Reserved field, only for syntax compatibility.<br> TRUE: Do not update execution plans.<br> FALSE: Update execution plans.<br> DBMS_STATS.AUTO_INVALIDATE: The database decides on its own when to update cached execution plans.|

***Example***

```plsql
exec DBMS_STATS.GATHER_DATABASE_STATS(estimate_percent=>1, degree=>8);
exec DBMS_STATS.GATHER_DATABASE_STATS(options=>'GATHER AUTO', estimate_percent=>1, degree=>2, method_opt=>'FOR ALL COLUMNS SIZE AUTO', granularity=>'AUTO', cascade=>TRUE, gather_sys=>FALSE, block_sample=>FALSE);
```

### FLUSH\_DATABASE\_MONITORING\_INFO

```plsql
DBMS_STATS.FLUSH_DATABASE_MONITORING_INFO
```

This procedure is used to persist changes to tables.

When real-time statistics collection is enabled, this procedure also persists real-time statistics.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
exec DBMS_STATS.FLUSH_DATABASE_MONITORING_INFO;
```

## Locking/Unlocking Statistics

### LOCK\_PARTITION\_STATS

```plsql
DBMS_STATS.LOCK_PARTITION_STATS (
    ownname             VARCHAR,
    tabname             VARCHAR,
    partname            VARCHAR
);
```

This procedure is used to lock the statistics of a specific partition in a table; once locked, the statistics for that partition will not be modified.

This procedure is not applicable to composite partition tables.

|Parameter |Description |
| :------- | :----- |
| ownname  | Username |
| tabname  | Table name |
| partname | Partition name |

***Example***

```plsql
exec DBMS_STATS.LOCK_PARTITION_STATS('SALES', 'SALES_INFO_RANGE', 'P_SALES_INFO_RANGE_1');
```

### LOCK\_TABLE\_STATS

```plsql
DBMS_STATS.LOCK_TABLE_STATS (
    ownname             VARCHAR,
    tabname             VARCHAR
);
```

This procedure is used to lock the statistics of a specific table; once locked, the statistics for that table will not be modified.

|Parameter |Description |
| :------ | :----- |
| ownname | Username |
| tabname | Table name |

***Example***

```plsql
exec DBMS_STATS.LOCK_TABLE_STATS('SALES', 'SALES_INFO');
```

### LOCK\_SCHEMA\_STATS

```plsql
DBMS_STATS.LOCK_SCHEMA_STATS (
    ownname             VARCHAR
);
```

This procedure is used to lock all tables' statistics under a specific schema; once locked, the statistics for all tables in that schema will not be modified. However, newly created tables in that schema thereafter are not locked.

|Parameter |Description |
| :------ | :----- |
| ownname | Username |

***Example***

```plsql
exec DBMS_STATS.LOCK_SCHEMA_STATS('SALES');
```

### UNLOCK\_PARTITION\_STATS

```plsql
DBMS_STATS.UNLOCK_PARTITION_STATS (
    ownname             VARCHAR,
    tabname             VARCHAR,
    partname            VARCHAR
);
```

This procedure is used to unlock the statistics of a specific partition in a table.

|Parameter |Description |
| :------- | :----- |
| ownname  | Username |
| tabname  | Table name |
| partname | Partition name |

***Example***

```plsql
exec DBMS_STATS.UNLOCK_PARTITION_STATS('SALES', 'SALES_INFO', 'P_SALES_INFO_1');
```

### UNLOCK\_TABLE\_STATS

```plsql
DBMS_STATS.UNLOCK_TABLE_STATS (
    ownname             VARCHAR,
    tabname             VARCHAR
);
```

This procedure is used to unlock the statistics of a specific table.

|Parameter |Description |
| :------ | :----- |
| ownname | Username |
| tabname | Table name |

***Example***

```plsql
exec DBMS_STATS.UNLOCK_TABLE_STATS('SALES', 'SALES_INFO');
```

### UNLOCK\_SCHEMA\_STATS

```plsql
DBMS_STATS.UNLOCK_SCHEMA_STATS (
    ownname             VARCHAR
);
```

This procedure is used to unlock all tables' statistics under a specific schema.

|Parameter |Description |
| :------ | :----- |
| ownname | Username |

***Example***

```plsql
exec DBMS_STATS.UNLOCK_SCHEMA_STATS('SALES');
```

<span id="statoption" name="statoption" class="yaslink"></span>

## Setting Statistics Options

When invoking statistics collection programs, YashanDB provides a series of options (such as partition granularity, sampling rate, etc.) for users to specify, in order to obtain more accurate and usable data. The values of these options can be set in the following ways:

- As parameters to the collection program during invocation, this method has the highest priority.
- Through the SET_*_PREFS program for general settings; when the option parameter is not specified or is NULL, the system uses this general setting value.
- If values are not specified using the above two methods, the system provides default values.

Once an option is set by the SET_*_PREFS program, it can be viewed through ALL_TAB_STAT_PREFS, DBA_TAB_STAT_PREFS, USER_TAB_STAT_PREFS views.

The specific options are listed in the table below:

|Option |Meaning |System Default Value |
| ------------------------- | ---------------------------- | ------------------------- |
| STALE_PERCENT             | Threshold for statistics becoming stale, should be >= 0; higher values make it less likely to become stale. When set to 0, statistics will always become stale.| 0.1                       |
| CASCADE                   | Whether to collect index statistics when collecting table statistics                | FALSE                     |
| DEGREE                    | Degree of parallelism for statistics collection; when set to DBMS_STATS.AUTO_DEGREE, the database will decide parallelism automatically| DBMS_STATS.AUTO_DEGREE                         |
| ESTIMATE_PERCENT          | Sampling rate, must be in the range of DBMS_STATS.AUTO_SAMPLE_SIZE or 0 or within [0.0001,1]; when set to DBMS_STATS.AUTO_SAMPLE_SIZE or 0, the database will decide the sampling rate automatically| DBMS_STATS.AUTO_SAMPLE_SIZE                         |
| GRANULARITY               | Partition table collection strategy:<br/>* ALL: collect statistics for both table and partition <br/>* GLOBAL: collect statistics for table only <br/>* GLOBAL AND PARTITION: collect statistics for both table and partition <br/>* PARTITION: collect statistics for partition only <br/>* SUBPARTITION: keep this option; collect statistics for subpartitions only <br/>* AUTO: auto-determined by type of partition | GLOBAL                    |
| METHOD_OPT                | Statistics collection options:<br/>* FOR ALL [INDEXED] COLUMNS [size_clause]: collect statistics for all columns or indexed columns <br/>* FOR COLUMNS [column_clause] [size_clause]: collect statistics for specified columns  <br/><br/>column_clause: (col1,col2,col3)<br/>size_clause: SIZE {integer|AUTO} to specify histogram information <br/>* integer: number of buckets for histogram, range [1,2048] <br/>* AUTO: the database decides whether to generate histogram<br/><br/>SET_SCHEMA_PREFS, SET_DATABASE_PREFS, SET_GLOBAL_PREFS can only set FOR ALL [INDEXED] COLUMNS [size_clause]| FOR ALL COLUMNS SIZE AUTO |
| OPTIONS                   | Database statistics collection options:<br/>* GATHER: collect statistics for all tables  <br/>* GATHER AUTO: default option, the database decides which tables to collect <br/>* GATHER STALE: only collect stale statistics <br/>* GATHER EMPTY: only collect empty statistics <br><br/>This option can only be set by SET_GLOBAL_PREFS.| GATHER                    |
| APPROXIMATE_NDV_ALGORITHM | Option to choose the distinct value estimation algorithm:<br/>* STREAM BASED: use HyperLogLog algorithm to estimate distinct values. This algorithm is active only under row sampling <br/>* SAMPLING BASED: use Shlosser's Estimator algorithm to estimate distinct values<br/><br/>This option can only be set by SET_GLOBAL_PREFS| SAMPLING BASED            |
|no_invalidate | Whether to update execution plans in database cache after gathering Statistics. Reserved field, only for syntax compatibility.<br> TRUE: Do not update execution plans.<br> FALSE: Update execution plans.<br> DBMS_STATS.AUTO_INVALIDATE: The database decides on its own when to update cached execution plans.|FALSE |


### SET\_TABLE\_PREFS

```plsql
DBMS_STATS.SET_TABLE_PREFS (
    ownname             VARCHAR,
    tabname             VARCHAR,
    pname               VARCHAR,
    pvalue              VARCHAR
);
```

This procedure is used to set the statistics options for a table, allowing users to control the statistics collection and expiration behavior of the table.

|Parameter |Description |
| :------ | :------------- |
| ownname | Username         |
| tabname | Table name           |
| pname   | Statistics option name |
| pvalue  | Statistics option value |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
exec DBMS_STATS.SET_TABLE_PREFS('SALES', 'SALES_INFO', 'DEGREE', '4');
```

### SET\_SCHEMA\_PREFS

```plsql
DBMS_STATS.SET_SCHEMA_PREFS (
    ownname             VARCHAR,
    pname               VARCHAR,
    pvalue              VARCHAR
);
```

This procedure is used to set the statistics options for all current tables under a schema; this does not apply to newly created tables thereafter.

|Parameter |Description |
| :------ | :------------- |
| ownname | Username         |
| pname   | Statistics option name |
| pvalue  | Statistics option value |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
exec DBMS_STATS.SET_SCHEMA_PREFS('SALES', 'DEGREE', '4');
```

### SET\_DATABASE\_PREFS

```plsql
DBMS_STATS.SET_DATABASE_PREFS (
    pname               VARCHAR,
    pvalue              VARCHAR,
    add_sys             BOOLEAN
);
```

This procedure is used to set the statistics options for all current tables in the database; this does not apply to newly created tables thereafter.

|Parameter |Description |
| :------ | :--------------------------------------------- |
| pname   | Statistics option name                                 |
| pvalue  | Statistics option value                                 |
| add_sys | Whether to set the statistics option for tables under SYS user; defaults to FALSE |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
exec DBMS_STATS.SET_DATABASE_PREFS('DEGREE', '4', FALSE);
```

### SET\_GLOBAL\_PREFS

```plsql
DBMS_STATS.SET_GLOBAL_PREFS (
    pname               VARCHAR,
    pvalue              VARCHAR
);
```

This procedure is used to set global statistics options, which apply to existing tables with that option unset and all subsequently created tables.

|Parameter |Description |
| :----- | :------------- |
| pname  | Statistics option name |
| pvalue | Statistics option value |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
exec DBMS_STATS.SET_GLOBAL_PREFS('DEGREE', '4');
```

### RESET\_GLOBAL\_PREF\_DEFAULTS

```plsql
DBMS_STATS.RESET_GLOBAL_PREF_DEFAULTS;
```

This procedure is used to reset all global statistics options to their default values.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
exec DBMS_STATS.RESET_GLOBAL_PREF_DEFAULTS;
```

## Deleting Statistics Options

### DELETE\_TABLE\_PREFS

```plsql
DBMS_STATS.DELETE_TABLE_PREFS (
    ownname             VARCHAR,
    tabname             VARCHAR,
    pname               VARCHAR
);
```

This procedure is used to delete the statistics options for a table, allowing users to control the statistics collection and expiration behavior of the table.

|Parameter |Description |
| :------- | :----- |
| ownname  | Username |
| tabname  | Table name |
| pname    | Statistics option name |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
exec DBMS_STATS.DELETE_TABLE_PREFS('SALES', 'SALES_INFO_RANGE', 'DEGREE');
```

### DELETE\_SCHEMA\_PREFS

```plsql
DBMS_STATS.DELETE_SCHEMA_PREFS (
    ownname             VARCHAR,
    pname               VARCHAR
);
```

This procedure is used to delete the current statistics options for all tables under a certain schema, allowing users to control the statistics collection and expiration behavior of the tables.

|Parameter |Description |
| :------- | :----- |
| ownname  | Username |
| pname    | Statistics option name |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
exec DBMS_STATS.DELETE_SCHEMA_PREFS('SALES', 'DEGREE');
```

## Retrieving the Value of Statistics Options

### GET\_PREFS

```plsql
DBMS_STATS.GET_PREFS (
    pname               VARCHAR,
    ownname             VARCHAR DEFAULT NULL,
    tabname             VARCHAR DEFAULT NULL)
RETURN VARCHAR;
```

This procedure is used to retrieve the value of a table's statistics option.

|Parameter |Description |
| :------- | :----- |
| pname    | Statistics option name |
| ownname  | Username |
| tabname  | Table name |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
SELECT DBMS_STATS.GET_PREFS('DEGREE', 'SALES', 'SALES_INFO_RANGE') FROM DUAL;
```

## Importing and Exporting Statistics

<span id="createstattable" name="createstattable" class="yaslink"></span>

### CREATE\_STAT\_TABLE

```plsql
DBMS_STATS.CREATE_STAT_TABLE (
    ownname          VARCHAR,
    stattab          VARCHAR,
    tblspace         VARCHAR DEFAULT NULL,
    global_temporary BOOLEAN DEFAULT FALSE
);
```
This procedure is used to create a special table for storing statistics, i.e., a statistics table.

It is recommended to maintain the statistics table (including addition, deletion, etc.) only through related procedures under the advanced package DBMS_STATS.

|Parameter |Description |
| :--------------- |:------------------------------------------|
| ownname          | Username                                       |
| stattab          | Statistics table name                                    |
| tblspace         | Tablespace name. If NULL is passed, the user's default tablespace is used        |
| global_temporary | Whether it is a global temporary table; the data of global temporary tables cannot be exported; please avoid using global temporary tables for exporting statistics |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
EXEC DBMS_STATS.CREATE_STAT_TABLE('SALES', 'STAT_TAB', global_temporary=>TRUE);
```

<span id="dropstattable" name="dropstattable" class="yaslink"></span>

### DROP\_STAT\_TABLE

```plsql
DBMS_STATS.DROP_STAT_TABLE (
    ownname          VARCHAR,
    stattab          VARCHAR
);
```

This procedure is used to delete the statistics table created by `CREATE_STAT_TABLE`.

|Parameter |Description |
| :------ | :----------- |
| ownname | Username       |
| stattab | Statistics table name |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
EXEC DBMS_STATS.DROP_STAT_TABLE('SALES', 'STAT_TAB');
```

<span id="exporttablestats" name="exporttablestats" class="yaslink"></span>

### EXPORT\_TABLE\_STATS

```plsql
DBMS_STATS.EXPORT_TABLE_STATS (
    ownname         VARCHAR, 
    tabname         VARCHAR, 
    partname        VARCHAR DEFAULT NULL,
    stattab         VARCHAR, 
    statid          VARCHAR DEFAULT NULL,
    cascade         BOOLEAN DEFAULT TRUE,
    statown         VARCHAR DEFAULT NULL,
    stat_category   VARCHAR DEFAULT 'OBJECT_STATS'
);
```
This procedure is used to export table statistics into a statistics table, defaulting to export both column and index statistics.

|Parameter |Description |
| :------------ | :----------------------------------------------------------- |
| ownname       | Username                                                       |
| tabname       | Table name                                                         |
| partname      | Partition name                                                       |
| stattab       | Statistics table name                                                 |
| statid        | Statistics identifier, used to identify different sets of statistics                     |
| cascade       | Whether to export column and index statistics                                |
| statown       | Username of the statistics table                                           |
| stat_category | Category of exported statistics. Currently only supports OBJECT_STATS, i.e., table, column, and index statistics  |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
-- Create statistics table STAT_TAB
EXEC DBMS_STATS.CREATE_STAT_TABLE('SALES', 'STAT_TAB', global_temporary=>TRUE);
-- Export statistics from SALES.SALES_INFO_RANGE table, columns and indexes to STAT_TAB with identifier STATS_EXAMPLE1
EXEC DBMS_STATS.EXPORT_TABLE_STATS('SALES', 'SALES_INFO_RANGE', stattab=>'STAT_TAB', statid=>'STATS_EXAMPLE1');
```

<span id="exportcolumnstats" name="exporttablestats" class="yaslink"></span>

### EXPORT\_COLUMN\_STATS

```plsql
DBMS_STATS.EXPORT_COLUMN_STATS (
    ownname         VARCHAR, 
    tabname         VARCHAR, 
    colname         VARCHAR,
    partname        VARCHAR DEFAULT NULL,
    stattab         VARCHAR, 
    statid          VARCHAR DEFAULT NULL,
    statown         VARCHAR DEFAULT NULL,
);
```

This procedure is used to export column statistics into a statistics table.

|Parameter |Description |
| :------- | :--------------------------------------- |
| ownname  | Username                                   |
| tabname  | Table name                                     |
| colname  | Column name                                     |
| partname | Partition name                                   |
| stattab  | Statistics table name                             |
| statid   | Statistics identifier, used to identify different sets of statistics |
| statown  | Username of the statistics table                       |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
EXEC DBMS_STATS.EXPORT_COLUMN_STATS('SALES', 'SALES_INFO_RANGE', 'product', stattab=>'STAT_TAB', statid=>'STATS_EXAMPLE1');
```

<span id="exportindexstats" name="exportindexstats" class="yaslink"></span>

### EXPORT\_INDEX\_STATS

```plsql
DBMS_STATS.EXPORT_INDEX_STATS (
    ownname         VARCHAR, 
    indname         VARCHAR, 
    partname        VARCHAR DEFAULT NULL,
    stattab         VARCHAR, 
    statid          VARCHAR DEFAULT NULL,
    statown         VARCHAR DEFAULT NULL,
);
```

This procedure is used to export index statistics into a statistics table.

|Parameter |Description |
| :------- | :--------------------------------------- |
| ownname  | Username                                   |
| indname  | Index name                                   |
| partname | Partition name                                   |
| stattab  | Statistics table name                             |
| statid   | Statistics identifier, used to identify different sets of statistics |
| statown  | Username of the statistics table                       |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
EXEC DBMS_STATS.EXPORT_INDEX_STATS('SALES', 'idx_sales_info_1', stattab=>'STAT_TAB', statid=>'STATS_EXAMPLE1');
```

<span id="exportschemastats" name="exportschemastats" class="yaslink"></span>

### EXPORT\_SCHEMA\_STATS

```plsql
DBMS_STATS.EXPORT_SCHEMA_STATS (
    ownname         VARCHAR, 
    stattab         VARCHAR, 
    statid          VARCHAR DEFAULT NULL,
    statown         VARCHAR DEFAULT NULL,
    stat_category   VARCHAR DEFAULT 'OBJECT_STATS'
);
```

This procedure is used to export all table statistics under a schema into a statistics table.

|Parameter |Description |
| :------------ | :----------------------------------------------------------- |
| ownname       | Username                                                       |
| stattab       | Statistics table name                                                 |
| statid        | Statistics identifier, used to identify different sets of statistics                     |
| statown       | Username of the statistics table                                           |
| stat_category | Category of exported statistics. Currently only supports OBJECT_STATS, i.e., table, column, and index statistics  |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
EXEC DBMS_STATS.EXPORT_SCHEMA_STATS('SALES', stattab=>'STAT_TAB', statid=>'STATS_EXAMPLE1');
```

<span id="importtablestats" name="importtablestats" class="yaslink"></span>

### IMPORT\_TABLE\_STATS

```plsql
DBMS_STATS.IMPORT_TABLE_STATS (
    ownname         VARCHAR, 
    tabname         VARCHAR, 
    partname        VARCHAR DEFAULT NULL,
    stattab         VARCHAR, 
    statid          VARCHAR DEFAULT NULL,
    cascade         BOOLEAN DEFAULT TRUE,
    statown         VARCHAR DEFAULT NULL,
    force           BOOLEAN DEFAULT FALSE,
    stat_category   VARCHAR DEFAULT 'OBJECT_STATS'
);
```

This procedure is used to import table statistics from a statistics table, defaulting to import both column and index statistics.

|Parameter |Description |
| :------------ | :----------------------------------------------------------- |
| ownname       | Username                                                       |
| tabname       | Table name                                                         |
| partname      | Partition name                                                       |
| stattab       | Statistics table name                                                 |
| statid        | Statistics identifier, used to identify different sets of statistics                     |
| cascade       | Whether to import column and index statistics                                |
| statown       | Username of the statistics table                                           |
| force         | Whether to forcefully import statistics, even if they are locked                     |
| stat_category | Category of imported statistics. Currently only supports OBJECT_STATS, i.e., table, column, and index statistics  |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
EXEC DBMS_STATS.IMPORT_TABLE_STATS('SALES', 'SALES_INFO_RANGE', stattab=>'STAT_TAB', statid=>'STATS_EXAMPLE1');
```

<span id="importcolumnstats" name="importcolumnstats" class="yaslink"></span>

### IMPORT\_COLUMN\_STATS

```plsql
DBMS_STATS.IMPORT_COLUMN_STATS (
    ownname         VARCHAR, 
    tabname         VARCHAR, 
    colname         VARCHAR,
    partname        VARCHAR DEFAULT NULL,
    stattab         VARCHAR, 
    statid          VARCHAR DEFAULT NULL,
    statown         VARCHAR DEFAULT NULL,
    force           BOOLEAN DEFAULT FALSE
);
```

This procedure is used to import column statistics from a statistics table.

|Parameter |Description |
| :------- | :--------------------------------------- |
| ownname  | Username                                   |
| tabname  | Table name                                     |
| colname  | Column name                                     |
| partname | Partition name                                   |
| stattab  | Statistics table name                             |
| statid   | Statistics identifier, used to identify different sets of statistics |
| statown  | Username of the statistics table                       |
| force    | Whether to forcefully import statistics, even if they are locked |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
EXEC DBMS_STATS.IMPORT_COLUMN_STATS('SALES', 'SALES_INFO_RANGE', 'product', stattab=>'STAT_TAB', statid=>'STATS_EXAMPLE1');
```

<span id="importindexstats" name="importindexstats" class="yaslink"></span>

### IMPORT\_INDEX\_STATS

```plsql
DBMS_STATS.IMPORT_INDEX_STATS (
    ownname         VARCHAR, 
    indname         VARCHAR, 
    partname        VARCHAR DEFAULT NULL,
    stattab         VARCHAR, 
    statid          VARCHAR DEFAULT NULL,
    statown         VARCHAR DEFAULT NULL,
    force           BOOLEAN DEFAULT FALSE
);
```

This procedure is used to import index statistics from a statistics table.

|Parameter |Description |
| :------- | :--------------------------------------- |
| ownname  | Username                                   |
| indname  | Index name                                   |
| partname | Partition name                                   |
| stattab  | Statistics table name                             |
| statid   | Statistics identifier, used to identify different sets of statistics |
| statown  | Username of the statistics table                       |
| force    | Whether to forcefully import statistics, even if they are locked |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
EXEC DBMS_STATS.IMPORT_INDEX_STATS('SALES', 'idx_sales_info_1', stattab=>'STAT_TAB', statid=>'STATS_EXAMPLE1');
```

<span id="importschemastats" name="importschemastats" class="yaslink"></span>

### IMPORT\_SCHEMA\_STATS

```plsql
DBMS_STATS.IMPORT_SCHEMA_STATS (
    ownname         VARCHAR, 
    stattab         VARCHAR, 
    statid          VARCHAR DEFAULT NULL,
    statown         VARCHAR DEFAULT NULL,
    force           BOOLEAN DEFAULT FALSE,
    stat_category   VARCHAR DEFAULT 'OBJECT_STATS'
);
```

This procedure is used to import all table statistics under a schema from a statistics table.

|Parameter |Description |
| :------------ | :----------------------------------------------------------- |
| ownname       | Username                                                       |
| stattab       | Statistics table name                                                 |
| statid        | Statistics identifier, used to identify different sets of statistics                     |
| statown       | Username of the statistics table                                           |
| force         | Whether to forcefully import statistics, even if they are locked                     |
| stat_category | Category of imported statistics. Currently only supports OBJECT_STATS, i.e., table, column, and index statistics  |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
EXEC DBMS_STATS.IMPORT_SCHEMA_STATS('SALES', stattab=>'STAT_TAB', statid=>'STATS_EXAMPLE1');
```

## Converting Statistics Values

### CONVERT\_RAW\_VALUE

```plsql
DBMS_STATS.CONVERT_RAW_VALUE (
    rawval     RAW,
    resval OUT FLOAT/DOUBLE/DATE/NUMBER/VARCHAR
);
```

This procedure is used to convert minimum values, maximum values, or histogram endpoints of columns into specific data type values.

|Parameter |Description |
| :------- | :----- |
| rawval   | Minimum value, maximum value, or endpoint value of the histogram |
| resval   | Value converted into a specific data type |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
SET serveroutput ON
DECLARE a NUMBER;
BEGIN
DBMS_STATS.CONVERT_RAW_VALUE('64', a);
DBMS_OUTPUT.PUT_LINE(a);
END;
/
```

## Copying Statistics Values

### COPY\_TABLE\_STATS

```plsql
DBMS_STATS.COPY_TABLE_STATS (
    ownname             VARCHAR,
    tabname             VARCHAR,
    srcpartname         VARCHAR,
    dstpartname         VARCHAR,
    scale_factor        NUMBER  DEFAULT 1,
    force               BOOLEAN DEFAULT FALSE
);
```

This procedure is used to copy statistics from a duplicated table partition to another partition, including column and local index statistics.

The procedure updates the minimum and maximum values in the target partition as follows:

-  If the target partition type is HASH, the target partition's minimum and maximum values are the same as the source partition.
- If the target partition type is LIST, the behavior depends on the settings of the target partition:
  - If the target partition is a NOT DEFAULT partition:
    - The target partition's minimum value is set to the minimum value in the target partition's value list.
    - The target partition's maximum value is set to the maximum value in the target partition's value list.
  - If the target partition is a DEFAULT partition:
    - The target partition's minimum value is set to the source partition's minimum value.
    - The target partition's maximum value is set to the source partition's maximum value.
- If the target partition type is RANGE:
  - For the first partition column:
    - If the target partition is the first partition, set the target partition's minimum value to the upper bound of the target partition.
    - If the target partition is not the first partition, set the target partition's minimum value to the upper bound of the previous partition.
    - If the target partition's upper bound is MAXVALUE, set the target partition's maximum value to the upper bound of the previous partition.
    - If the target partition's upper bound is not MAXVALUE, set the target partition's maximum value to the upper bound of the target partition.
  - For the second and subsequent partition columns:
    - Set the maximum value for the target partition column to the greater of the upper bound of the target partition and the maximum value of the source partition column.
    - If the upper bounds of the previous partition column for the target partition and the previous target partition are the same, set the maximum value for the target partition column to the upper bound of the target partition (maximum value of the source partition column).
  - For all partition columns:
    - If the minimum and maximum values of the source partition column are equal, both equal the lower bound of the source partition, and it has a single distinct value, the minimum and maximum values of the target partition column are both set to the lower bound of the target partition.
- If the updated minimum and maximum values of a column differ and distinct equals 1, the distinct value will be updated to 2.

|Parameter |Description |
| :--- | :---- |
| ownname     | Username |
| tabname     | Table name |
| srcpartname | Source partition name |
| dstpartname | Target partition name |
| scale_factor| Scale factor for statistics such as numrows, numblks of the target partition  |
| force       | Whether to forcibly copy statistics to locked partitions  |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
exec dbms_stats.copy_table_stats('SALES', 'SALES_INFO_RANGE', 'P_SALES_INFO_RANGE_1', 'P_SALES_INFO_RANGE_2', 1, false);
```

## Statistics History Collection Records

### PURGE\_STATS

```plsql
DBMS_STATS.PURGE_STATS (
    before_timestamp  TIMESTAMP,
);
```

This procedure clears the history collection records for statistics prior to the specified timestamp.

|Parameter |Description |
| :--- | :---- |
| before_timestamp | Purge timestamp |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
exec dbms_stats.purge_stats(sysdate);
```

### ALTER\_STATS\_HISTORY\_RETENTION

```plsql
DBMS_STATS.ALTER_STATS_HISTORY_RETENTION (
    retention  number,
);
```

This procedure sets the retention period for statistics history collection records (in days), default value is 7.

|Parameter |Description |
| :--- | :---- |
| retention | Retention period, valid values are NULL or [1, 365]; NULL will set retention to default value |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
exec dbms_stats.alter_stats_history_retention(31);
```

### GET\_STATS\_HISTORY\_RETENTION

```plsql
DBMS_STATS.GET_STATS_HISTORY_RETENTION 
RETURN VARCHAR;
```

This procedure fetches the retention period for statistics history collection records (in days).

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
SELECT dbms_stats.get_stats_history_retention FROM dual;
```

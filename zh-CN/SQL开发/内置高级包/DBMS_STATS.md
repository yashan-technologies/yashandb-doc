DBMS_STATS包提供了一组变量、函数和存储过程，用于管理优化器的统计信息。

除收集table/schema/database统计信息、加锁/解锁partition/table/schema统计信息外，其他函数/存储过程不适用于存算一体分布式集群部署。

## 手动设置统计信息

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

此存储过程用于手动设置表/AC或者分区的统计信息，旧的统计信息将会被覆盖，主要在测试需要或者统计信息修正场景中使用。

此存储过程不适用于组合分区表。

|  参数| 描述|
| :--- | :---- |
| ownname| 用户名 |
| tabname| 表/AC名 |
| partname| 分区名|
| numrows| 表/AC或者分区的行数 |
| numblks| 表/AC或者分区的数据块数 |
| avgrlen| 表/AC或者分区的平均行长度 |

示例（单机/共享集群/分布式集群部署）
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

此存储过程用于手动设置索引或者分区的统计信息，旧的统计信息将会被覆盖，主要在测试需要或者统计信息修正场景中使用。

此存储过程不适用于组合分区表的索引信息。

|  参数| 描述|
| :----------- | :----------------------------- |
| ownname      | 用户名                         |
| indname      | 索引名                         |
| partname     | 索引分区名                     |
| numrows      | 索引键个数                     |
| numlblks     | 叶子块个数                     |
| numdist      | 索引键唯一值个数               |
| numfirstdist | 组合索引第一列的键唯一值个数   |
| avglblk      | 平均每个叶子块包含键的个数     |
| avgdblk      | 平均每个叶子块指向数据块的个数 |
| clstfct      | 索引的聚集因子                 |
| indlevel     | BTree的高度                    |
| dist2keys    | 组合索引前两列的键唯一值个数   |
| dist3keys    | 组合索引前三列的键唯一值个数   |
| dist4keys    | 组合索引前四列的键唯一值个数   |

示例（单机/共享集群/分布式集群部署）

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

此存储过程用于手动设置表/AC或者分区的列统计信息，旧的统计信息将会被覆盖，主要在测试需要或者统计信息修正场景中使用。

此存储过程不适用于组合分区表。

|  参数| 描述|
| :--- | :---- |
| ownname| 用户名 |
| tabname| 表/AC名 |
| colname| 列名|
| partname| 分区名|
| distcnt | 列distinct数量 |
| density| 列density，取值范围为[0,1] |
| nullcnt| 列NULL值数量 |
| avgclen| 平均列长度|

示例（单机/共享集群/分布式集群部署）
```plsql
BEGIN
  DBMS_STATS.SET_COLUMN_STATS( 'SALES', 'SALES_INFO_RANGE', 'AMOUNT', 'P_SALES_INFO_RANGE_1', 10, 0.2, 15, 10);
END;
/
```

## 删除统计信息

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

此存储过程用于手动删除表的统计信息，主要在清理不准确的统计信息场景中使用。

|  参数| 描述|
| :--- | :---- |
| ownname| 用户名 |
| tabname| 表/AC名 |
| partname| 分区名 |
| cascade_parts| 是否删除与表相关联的分区的统计信息 |
| cascade_columns| 是否删除与表相关联的列的统计信息 |
| cascade_indexes| 是否删除与表相关联的索引的统计信息 |
| force| 是否强制删除被锁定的统计信息 |

示例（单机/共享集群/分布式集群部署）
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

此存储过程用于手动删除索引的统计信息，主要在清理不准确的统计信息场景中使用。

此存储过程不适用于组合分区表的索引信息。

|  参数| 描述|
| :--- | :---- |
| ownname| 用户名 |
| indname| 索引名 |
| partname| 索引分区名 |
| cascade_parts| 是否删除与索引相关联的分区的统计信息 |
| force| 是否强制删除被锁定的统计信息 |

示例（单机/共享集群/分布式集群部署）
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

此存储过程用于手动删除某个schema下所有对象（表、AC、列、索引）的统计信息，主要在清理不准确的统计信息场景中使用。

|  参数| 描述|
| :--- | :---- |
| ownname| 用户名 |
| force| 是否强制删除被锁定的统计信息 |

示例（单机/共享集群/分布式集群部署）
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

此存储过程用于手动删除列的统计信息，主要在清理不准确的统计信息场景中使用。

|  参数| 描述|
| :--- | :---- |
| ownname| 用户名 |
| tabname| 表/AC名 |
| colname| 列名|
| partname| 分区名|
| type| 删除类型：<br/>'ALL'：同时删除column统计信息和直方图<br/>'HISTOGRAM'：只删除直方图 |
| cascade_parts| 是否删除与列相关联的分区的统计信息 |
| force| 是否强制删除被锁定的统计信息 |

示例（单机/共享集群/分布式集群部署）
```plsql
BEGIN
    DBMS_STATS.DELETE_COLUMN_STATS('SALES', 'SALES_INFO_RANGE', 'AMOUNT', 'P_SALES_INFO_RANGE_1', 'ALL', true, false);
END;
/
```

## 收集统计信息

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

此存储过程用于收集表/AC或分区的统计信息。

对于组合分区表，不可指定收集某个分区/子分区，仅收集其GLOBAL级统计信息。

|  参数| 描述|
| :--- | :---- |
| ownname| 用户名 |
| tabname| 表/AC名 |
| partname| 分区名 |
| estimate_percent| 采样率，请查阅[统计信息选项](#statoption) |
| block_sample|是否采用块级采样|
| method_opt| 列统计信息选项，请查阅[统计信息选项](#statoption) |
| degree| 并行度，对于大表增大并行度可以提升统计信息收集的效率，请查阅[统计信息选项](#statoption) |
| granularity| 分区统计粒度，请查阅[统计信息选项](#statoption) |
| cascade | 是否收集索引统计信息，请查阅[统计信息选项](#statoption) |
|no_invalidate | 控制统计信息收集更新后是否影响当前的执行计划。该语法仅作为语法兼容，无实际效果。<br> TRUE：当次收集的统计信息不用于更新缓存中的执行计划；<br> FALSE：当次收集的统计信息立刻用于更新缓存中的执行计划。<br>DBMS_STATS.AUTO_INVALIDATE：由数据库自行决定缓存执行计划的更新时间。|

> **Note**
>
> 分区统计信息可被收集，但不被优化器使用。

示例

```plsql
BEGIN
    DBMS_STATS.GATHER_TABLE_STATS('SALES', 'SALES_INFO_RANGE', estimate_percent=>0.5, degree=>8);
    DBMS_STATS.GATHER_TABLE_STATS('SALES', 'SALES_INFO_RANGE', partname=>'P_SALES_INFO_RANGE_1', estimate_percent=>0.2, block_sample=>FALSE, method_opt=>'FOR ALL COLUMNS SIZE AUTO', degree=>4, granularity=>'AUTO', CASCADE=>TRUE);
END;
/
```

### GATHER\_INDEX\_STATS

```plsql
-- GET_PREFS中的tabname为索引对应的表的名字
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

此存储过程用于收集索引或索引分区的统计信息。

对于组合分区索引，不可指定收集某个分区/子分区，仅收集其GLOBAL级统计信息。

|  参数| 描述|
| :--- | :---- |
| ownname| 用户名 |
| indname| 索引名 |
| partname| 索引分区名，NULL表示统计所有分区 |
| estimate_percent| 采样率百分比，请查阅[统计信息选项](#statoption) |
| degree| 并行度，请查阅[统计信息选项](#statoption) |
| granularity| 分区统计粒度，请查阅[统计信息选项](#statoption) |
|no_invalidate | 控制统计信息收集更新后是否影响当前的执行计划。该语法仅作为语法兼容，无实际效果。<br> TRUE：当次收集的统计信息不用于更新缓存中的执行计划；<br> FALSE：当次收集的统计信息立刻用于更新缓存中的执行计划。<br>DBMS_STATS.AUTO_INVALIDATE：由数据库自行决定缓存执行计划的更新时间。|

示例（单机/共享集群/分布式集群部署）
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

此存储过程用于收集指定用户下所有对象（表、AC、列、索引）的统计信息。

|  参数| 描述|
| :--------------- | :------------------------------------------------------ |
| ownname          | 用户名                                                  |
| estimate_percent | 采样率百分比，请查阅[统计信息选项](#statoption)         |
| block_sample     | 是否采用块级采样                                        |
| method_opt       | 列统计信息选项，请查阅[统计信息选项](#statoption)       |
| degree           | 并行度，请查阅[统计信息选项](#statoption)               |
| granularity      | 分区统计粒度，请查阅[统计信息选项](#statoption)         |
| cascade          | 是否收集索引统计信息，请查阅[统计信息选项](#statoption) |
| options          | 请查阅[统计信息选项](#statoption)                       |
|no_invalidate | 控制统计信息收集更新后是否影响当前的执行计划。该语法仅作为语法兼容，无实际效果。<br> TRUE：当次收集的统计信息不用于更新缓存中的执行计划；<br> FALSE：当次收集的统计信息立刻用于更新缓存中的执行计划。<br>DBMS_STATS.AUTO_INVALIDATE：由数据库自行决定缓存执行计划的更新时间。|

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

此存储过程用于收集数据库的统计信息。

|  参数| 描述|
| :--------------- | :------------------------------------------------------ |
| options          | 请查阅[统计信息选项](#statoption)                       |
| estimate_percent | 采样率，请查阅[统计信息选项](#statoption)               |
| degree           | 并行度，请查阅[统计信息选项](#statoption)               |
| method_opt       | 列统计信息选项，请查阅[统计信息选项](#statoption)       |
| granularity      | 分区统计粒度，请查阅[统计信息选项](#statoption)         |
| cascade          | 是否收集索引统计信息，请查阅[统计信息选项](#statoption) |
| gather_sys       | 是否收集系统表的统计信息                             |
| block_sample     | 是否采用块级采样                    |
|no_invalidate | 控制统计信息收集更新后是否影响当前的执行计划。该语法仅作为语法兼容，无实际效果。<br> TRUE：当次收集的统计信息不用于更新缓存中的执行计划；<br> FALSE：当次收集的统计信息立刻用于更新缓存中的执行计划。<br>DBMS_STATS.AUTO_INVALIDATE：由数据库自行决定缓存执行计划的更新时间。|

示例

```plsql
exec DBMS_STATS.GATHER_DATABASE_STATS(estimate_percent=>1, degree=>8);
exec DBMS_STATS.GATHER_DATABASE_STATS(options=>'GATHER AUTO', estimate_percent=>1, degree=>2, method_opt=>'FOR ALL COLUMNS SIZE AUTO', granularity=>'AUTO', cascade=>TRUE, gather_sys=>FALSE, block_sample=>FALSE);
```

### FLUSH\_DATABASE\_MONITORING\_INFO

```plsql
DBMS_STATS.FLUSH_DATABASE_MONITORING_INFO
```

此存储过程用于持久化表/AC的变化信息。

开启实时收集统计信息时，该存储过程还会持久化实时统计信息。

示例（单机/共享集群/分布式集群部署）

```plsql
exec DBMS_STATS.FLUSH_DATABASE_MONITORING_INFO;
```

## 锁定/解锁统计信息

### LOCK\_PARTITION\_STATS

```plsql
DBMS_STATS.LOCK_PARTITION_STATS (
    ownname             VARCHAR,
    tabname             VARCHAR,
    partname            VARCHAR
);
```

此存储过程用于锁定表中某个表分区的统计信息，锁定后该分区的统计信息不再被修改。

此存储过程不适用于组合分区表。

|  参数| 描述|
| :------- | :----- |
| ownname  | 用户名 |
| tabname  | 表名   |
| partname | 分区名 |

示例

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

此存储过程用于锁定某张表的统计信息，锁定后该表的统计信息不再被修改。

|  参数| 描述|
| :------ | :----- |
| ownname | 用户名 |
| tabname | 表名   |

示例

```plsql
exec DBMS_STATS.LOCK_TABLE_STATS('SALES', 'SALES_INFO');
```

### LOCK\_SCHEMA\_STATS

```plsql
DBMS_STATS.LOCK_SCHEMA_STATS (
    ownname             VARCHAR
);
```

此存储过程用于锁定某个schema下所有表的统计信息，锁定后该schema下所有表的统计信息不再被修改，但该schema下在此之后新建的表并不锁定。

|  参数| 描述|
| :------ | :----- |
| ownname | 用户名 |

示例

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

此存储过程用于解锁表中某个表分区的统计信息。

|  参数| 描述|
| :------- | :----- |
| ownname  | 用户名 |
| tabname  | 表名   |
| partname | 分区名 |

示例

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

此存储过程用于解锁某张表的统计信息。

|  参数| 描述|
| :------ | :----- |
| ownname | 用户名 |
| tabname | 表名   |

示例

```plsql
exec DBMS_STATS.UNLOCK_TABLE_STATS('SALES', 'SALES_INFO');
```

### UNLOCK\_SCHEMA\_STATS

```plsql
DBMS_STATS.UNLOCK_SCHEMA_STATS (
    ownname             VARCHAR
);
```

此存储过程用于解锁某个schema下所有表的统计信息。

|  参数| 描述|
| :------ | :----- |
| ownname | 用户名 |

示例

```plsql
exec DBMS_STATS.UNLOCK_SCHEMA_STATS('SALES');
```

<span id="statoption" name="statoption" class="yaslink"></span>

## 设置统计信息选项

在调用统计信息收集程序时，YashanDB提供一系列选项（例如分区粒度、采样率等）供用户指定，以获取更精准可用的数据，这些选项的值可通过如下方式设置：

- 作为收集程序的参数，在调用时指定，此种方式优先级最高。
- 通过SET_*_PREFS程序进行通用设定，当未指定选项参数，或选项参数为NULL时，系统使用此通用设定值。
- 当未按上述两种方式指定值时，由系统给出默认值。

其中，某个选项一旦由SET_*_PREFS程序设定，即可通过ALL_TAB_STAT_PREFS、DBA_TAB_STAT_PREFS、USER_TAB_STAT_PREFS视图查看该选项在表上对应的值。

具体选项如下表所示：

|  选项| 含义| 系统默认值|
| ------------------------- | ---------------------------- | ------------------------- |
| STALE_PERCENT             | 统计信息失效的变化率阈值，取值应大于等于0，数值越大越不容易失效。当取值为0时，表示统计信息一定会失效      | 0.1                       |
| CASCADE                   | 收集表的统计信息时，是否一并收集索引的统计信息               | FALSE                     |
| DEGREE                    | 统计信息收集的并行度，当取值为DBMS_STATS.AUTO_DEGREE时，将由数据库自动决定并行度           | DBMS_STATS.AUTO_DEGREE                         |
| ESTIMATE_PERCENT          | 采样率，取值为DBMS_STATS.AUTO_SAMPLE_SIZE或0或在[0.0001,1]之内。当取值为DBMS_STATS.AUTO_SAMPLE_SIZE或0时，将由数据库自动决定采样率 | DBMS_STATS.AUTO_SAMPLE_SIZE                         |
| GRANULARITY               | 分区表收集策略：<br/>\* ALL：收集表和分区<br/>\* GLOBAL：只收集表 <br/>\* GLOBAL AND PARTITION：收集表和分区<br/>\* PARTITION：只收集分区<br/>\* SUBPARTITION：保留选项。只收集子分区<br/>\* AUTO：由分区的类型自动决定 | GLOBAL                    |
| METHOD_OPT                | 统计信息收集选项：<br/>\* FOR ALL [INDEXED] COLUMNS [size_clause]：收集所有列或索引列的统计信息<br/>\* FOR COLUMNS [column_clause] [size_clause]：收集指定列的统计信息 <br/><br/>column_clause：(col1,col2,col3)<br/>size_clause：SIZE {integer&#124;AUTO}用于指定直方图信息 <br/>\* integer：直方图的bucket数量，范围为[1,2048]<br/>\* AUTO：由数据库决定是否生成直方图<br/><br/>SET_SCHEMA_PREFS、SET_DATABASE_PREFS、SET_GLOBAL_PREFS只能设置FOR ALL [INDEXED] COLUMNS [size_clause] | FOR ALL COLUMNS SIZE AUTO |
| OPTIONS                   | 数据库统计信息收集选项：<br/>\* GATHER：收集所有表的统计信息 <br/>\* GATHER AUTO：默认选项，由数据库决定收集的表 <br/>\* GATHER STALE：只收集无效的统计信息<br/>\* GATHER EMPTY：只收集空的统计信息<br><br/>该选项仅能通过SET_GLOBAL_PREFS设置。 | GATHER                    |
| APPROXIMATE_NDV_ALGORITHM | 选择distinct估计算法的选项：<br/>\* STREAM BASED：使用HyperLogLog算法估计distinct。该算法仅在行采样下生效 <br/>\* SAMPLING BASED：使用Shlosser's Estimator算法估计distinct<br/><br/>该选项仅能通过SET_GLOBAL_PREFS设置 | SAMPLING BASED            |
|no_invalidate | 控制统计信息收集更新后是否影响当前的执行计划。该语法仅作为语法兼容，无实际效果。<br> TRUE：当次收集的统计信息不用于更新缓存中的执行计划；<br> FALSE：当次收集的统计信息立刻用于更新缓存中的执行计划。<br>DBMS_STATS.AUTO_INVALIDATE：由数据库自行决定缓存执行计划的更新时间。|FALSE |


### SET\_TABLE\_PREFS

```plsql
DBMS_STATS.SET_TABLE_PREFS (
    ownname             VARCHAR,
    tabname             VARCHAR,
    pname               VARCHAR,
    pvalue              VARCHAR
);
```

此存储过程用于设置表的统计信息选项，方便用户控制表的统计信息收集、失效等行为。

|  参数| 描述|
| :------ | :------------- |
| ownname | 用户名         |
| tabname | 表名           |
| pname   | 统计信息选项名 |
| pvalue  | 统计信息选项值 |

示例（单机/共享集群/分布式集群部署）

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

此存储过程用于设置schema下当前所有表的统计信息选项，对后续新建的表不生效。

|  参数| 描述|
| :------ | :------------- |
| ownname | 用户名         |
| pname   | 统计信息选项名 |
| pvalue  | 统计信息选项值 |

示例（单机/共享集群/分布式集群部署）

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

此存储过程用于设置数据库下当前所有表的统计信息选项，对后续新建的表不生效。

|  参数| 描述|
| :------ | :--------------------------------------------- |
| pname   | 统计信息选项名                                 |
| pvalue  | 统计信息选项值                                 |
| add_sys | 是否设置SYS用户下表的统计信息选项，默认为FALSE |

示例（单机/共享集群/分布式集群部署）

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

此存储过程用于设置全局的统计信息选项，对现有没有设置该选项的表和后续新建的表生效。

|  参数| 描述|
| :----- | :------------- |
| pname  | 统计信息选项名 |
| pvalue | 统计信息选项值 |

示例（单机/共享集群/分布式集群部署）

```plsql
exec DBMS_STATS.SET_GLOBAL_PREFS('DEGREE', '4');
```

### RESET\_GLOBAL\_PREF\_DEFAULTS

```plsql
DBMS_STATS.RESET_GLOBAL_PREF_DEFAULTS;
```

此存储过程用于将所有全局的统计信息选项设置为默认值。

示例（单机/共享集群/分布式集群部署）

```plsql
exec DBMS_STATS.RESET_GLOBAL_PREF_DEFAULTS;
```

## 删除统计信息选项

### DELETE\_TABLE\_PREFS

```plsql
DBMS_STATS.DELETE_TABLE_PREFS (
    ownname             VARCHAR,
    tabname             VARCHAR,
    pname               VARCHAR
);
```

此存储过程用于删除表的统计信息选项，方便用户控制表的统计信息收集、失效等行为。

|  参数| 描述|
| :------- | :----- |
| ownname  | 用户名 |
| tabname  | 表名   |
| pname    | 统计信息选项名 |

示例（单机/共享集群/分布式集群部署）

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

此存储过程用于删除某个schema下当前所有表的统计信息选项，方便用户控制表的统计信息收集、失效等行为。

|  参数| 描述|
| :------- | :----- |
| ownname  | 用户名 |
| pname    | 统计信息选项名 |

示例（单机/共享集群/分布式集群部署）

```plsql
exec DBMS_STATS.DELETE_SCHEMA_PREFS('SALES', 'DEGREE');
```

## 获取统计信息选项的值

### GET\_PREFS

```plsql
DBMS_STATS.GET_PREFS (
    pname               VARCHAR,
    ownname             VARCHAR DEFAULT NULL,
    tabname             VARCHAR DEFAULT NULL)
RETURN VARCHAR;
```

此存储过程用于获取表的统计信息选项的值。

|  参数| 描述|
| :------- | :----- |
| pname    | 统计信息选项名 |
| ownname  | 用户名 |
| tabname  | 表名   |

示例（单机/共享集群/分布式集群部署）

```plsql
SELECT DBMS_STATS.GET_PREFS('DEGREE', 'SALES', 'SALES_INFO_RANGE') FROM DUAL;
```

## 统计信息导入导出

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
此存储过程用于创建专门存储统计信息的表，即统计信息表。

建议只通过DBMS_STATS高级包下的相关存储过程维护统计信息表，包括新增、删除等操作。

|  参数| 描述|
| :--------------- |:------------------------------------------|
| ownname          | 用户名                                       |
| stattab          | 统计信息表名                                    |
| tblspace         | 表空间名。传入NULL则使用用户默认表空间                     |
| global_temporary | 是否为全局临时表，全局临时表的数据无法导出，如果要导出统计信息请不要使用全局临时表 |

示例（单机/共享集群/分布式集群部署）

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

此存储过程用于删除`CREATE_STAT_TABLE`创建出来的统计信息表。

|  参数| 描述|
| :------ | :----------- |
| ownname | 用户名       |
| stattab | 统计信息表名 |

示例（单机/共享集群/分布式集群部署）

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
此存储过程用于导出表的统计信息到统计信息表中，默认同时会导出列和表上索引的统计信息。

|  参数| 描述|
| :------------ | :----------------------------------------------------------- |
| ownname       | 用户名                                                       |
| tabname       | 表名                                                         |
| partname      | 分区名                                                       |
| stattab       | 统计信息表名                                                 |
| statid        | 统计信息标识。用于标识不同的统计信息集合                     |
| cascade       | 是否导出列和表上索引的统计信息                               |
| statown       | 统计信息表的用户名                                           |
| stat_category | 导出的统计信息类别。当前只支持OBJECT_STATS，即表、列和索引的统计信息 |

示例（单机/共享集群/分布式集群部署）

```plsql
-- 创建统计信息表STAT_TAB
EXEC DBMS_STATS.CREATE_STAT_TABLE('SALES', 'STAT_TAB', global_temporary=>TRUE);
-- 导出SALES.SALES_INFO_RANGE下表、列和索引的统计信息到STAT_TAB中并标识为STATS_EXAMPLE1
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

此存储过程用于导出列统计信息到统计信息表中。

|  参数| 描述|
| :------- | :--------------------------------------- |
| ownname  | 用户名                                   |
| tabname  | 表名                                     |
| colname  | 列名                                     |
| partname | 分区名                                   |
| stattab  | 统计信息表名                             |
| statid   | 统计信息标识。用于标识不同的统计信息集合 |
| statown  | 统计信息表的用户名                       |

示例（单机/共享集群/分布式集群部署）

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

此存储过程用于导出索引统计信息到统计信息表中。

|  参数| 描述|
| :------- | :--------------------------------------- |
| ownname  | 用户名                                   |
| indname  | 索引名                                   |
| partname | 分区名                                   |
| stattab  | 统计信息表名                             |
| statid   | 统计信息标识。用于标识不同的统计信息集合 |
| statown  | 统计信息表的用户名                       |

示例（单机/共享集群/分布式集群部署）

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

此存储过程用于导出schema下所有表统计信息到统计信息表中。

|  参数| 描述|
| :------------ | :----------------------------------------------------------- |
| ownname       | 用户名                                                       |
| stattab       | 统计信息表名                                                 |
| statid        | 统计信息标识。用于标识不同的统计信息集合                     |
| statown       | 统计信息表的用户名                                           |
| stat_category | 导出的统计信息类别。当前只支持OBJECT_STATS，即表、列和索引的统计信息 |

示例（单机/共享集群/分布式集群部署）

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

此存储过程用于从统计信息表导入表的统计信息，默认同时会导入列和表上索引的统计信息。

|  参数| 描述|
| :------------ | :----------------------------------------------------------- |
| ownname       | 用户名                                                       |
| tabname       | 表名                                                         |
| partname      | 分区名                                                       |
| stattab       | 统计信息表名                                                 |
| statid        | 统计信息标识。用于标识不同的统计信息集合                     |
| cascade       | 是否导出列和表上索引的统计信息                               |
| statown       | 统计信息表的用户名                                           |
| force         | 是否强制导入统计信息，即使统计信息被锁定                     |
| stat_category | 导出的统计信息类别。当前只支持OBJECT_STATS，即表、列和索引的统计信息 |

示例（单机/共享集群/分布式集群部署）

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

此存储过程用于从统计信息表导入列统计信息。

|  参数| 描述|
| :------- | :--------------------------------------- |
| ownname  | 用户名                                   |
| tabname  | 表名                                     |
| colname  | 列名                                     |
| partname | 分区名                                   |
| stattab  | 统计信息表名                             |
| statid   | 统计信息标识。用于标识不同的统计信息集合 |
| statown  | 统计信息表的用户名                       |
| force    | 是否强制导入统计信息，即使统计信息被锁定 |

示例（单机/共享集群/分布式集群部署）

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

此存储过程用于从统计信息表导入索引统计信息。

|  参数| 描述|
| :------- | :--------------------------------------- |
| ownname  | 用户名                                   |
| indname  | 索引名                                   |
| partname | 分区名                                   |
| stattab  | 统计信息表名                             |
| statid   | 统计信息标识。用于标识不同的统计信息集合 |
| statown  | 统计信息表的用户名                       |
| force    | 是否强制导入统计信息，即使统计信息被锁定 |

示例（单机/共享集群/分布式集群部署）

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

此存储过程用于从统计信息表中导入schema下所有表统计信息。

|  参数| 描述|
| :------------ | :----------------------------------------------------------- |
| ownname       | 用户名                                                       |
| stattab       | 统计信息表名                                                 |
| statid        | 统计信息标识。用于标识不同的统计信息集合                     |
| statown       | 统计信息表的用户名                                           |
| force         | 是否强制导入统计信息，即使统计信息被锁定                     |
| stat_category | 导出的统计信息类别。当前只支持OBJECT_STATS，即表、列和索引的统计信息 |

示例（单机/共享集群/分布式集群部署）

```plsql
EXEC DBMS_STATS.IMPORT_SCHEMA_STATS('SALES', stattab=>'STAT_TAB', statid=>'STATS_EXAMPLE1');
```

## 转换统计信息值

### CONVERT\_RAW\_VALUE

```plsql
DBMS_STATS.CONVERT_RAW_VALUE (
    rawval     RAW,
    resval OUT FLOAT/DOUBLE/DATE/NUMBER/VARCHAR
);
```

此存储过程用于将列最小值、列最大值或者直方图端点值转换为特定数据类型的值。

|  参数| 描述|
| :------- | :----- |
| rawval   | 列最小值、列最大值或者直方图端点值 |
| resval   | 转换后的特定数据类型值 |

示例（单机/共享集群/分布式集群部署）

```plsql
SET serveroutput ON
DECLARE a NUMBER;
BEGIN
DBMS_STATS.CONVERT_RAW_VALUE('64', a);
DBMS_OUTPUT.PUT_LINE(a);
END;
/
```

## 复制统计信息值

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

此存储过程用于复制表分区的统计信息至另一分区，包括列和本地索引统计信息。

该过程按照以下方式更新目标分区中的最小值和最大值：

- 如果目标分区类型为HASH，则目标分区的最小值和最大值与源分区的相同。
- 如果目标分区类型为LIST，则行为取决于目标分区的设置：
  - 若目标分区是NOT DEFAULT分区，则：
    - 目标分区的最小值设置为目标分区值列表的最小值。
    - 目标分区的最大值设置为目标分区值列表的最大值。
  - 若目标分区是DEFAULT分区，则：
    - 目标分区的最小值设置为源分区的最小值。
    - 目标分区的最大值设置为源分区的最大值。
- 如果目标分区类型为RANGE，则：
  - 对于首个分区列，则：
    - 目标分区为首个分区，目标分区的最小值设置为目标分区的上界。
    - 目标分区不为首个分区，目标分区的最小值设置为目标分区前一个分区的上界。
    - 目标分区上界是MAXVALUE，目标分区的最大值设置为目标分区前一个分区的上界。
    - 目标分区上界不是MAXVALUE，目标分区的最大值设置为目标分区的上界。
  - 对第二个及后续分区列，则：
    - 目标分区列的最大值设置为目标分区上界和源分区列最大值中的较大者。
    - 若前一分区列在目标分区和目标分区前一个分区的上界相同，则将目标分区列的最大值设置为目标分区上界（源分区列最大值）。
  - 对于所有分区列，则：
    - 若源分区列的最小值等于最大值，且两者都等于源分区的下界，并且它具有单个distinct值，则目标分区列的最小值和最大值都设置为目标分区的下界。
- 若更新后的某列最小值和最大值不同，且disinct等于1，会将distinct更新为2。

|  参数| 描述|
| :--- | :---- |
| ownname     | 用户名 |
| tabname     | 表名 |
| srcpartname | 源分区名 |
| dstpartname | 目标分区名 |
| scale_factor| 缩放目标分区的numrows、numblks等统计信息 |
| force       | 是否强制复制统计信息至被锁定的分区 |

示例（单机/共享集群/分布式集群部署）

```plsql
exec dbms_stats.copy_table_stats('SALES', 'SALES_INFO_RANGE', 'P_SALES_INFO_RANGE_1', 'P_SALES_INFO_RANGE_2', 1, false);
```

## 统计信息历史收集记录

### PURGE\_STATS

```plsql
DBMS_STATS.PURGE_STATS (
    before_timestamp  TIMESTAMP,
);
```

此存储过程将清除指定时间点之前的统计信息历史收集记录。

|  参数| 描述|
| :--- | :---- |
| before_timestamp | 清理时间点 |

示例（单机/共享集群/分布式集群部署）

```plsql
exec dbms_stats.purge_stats(sysdate);
```

### ALTER\_STATS\_HISTORY\_RETENTION

```plsql
DBMS_STATS.ALTER_STATS_HISTORY_RETENTION (
    retention  number,
);
```

此存储过程将设置统计信息历史收集记录保留时间（以天为单位），默认值为7。

|  参数| 描述|
| :--- | :---- |
| retention | 保留时间，有效取值是NULL或[1, 365]，NULL会将保留时间更改为默认值 |

示例（单机/共享集群/分布式集群部署）

```plsql
exec dbms_stats.alter_stats_history_retention(31);
```

### GET\_STATS\_HISTORY\_RETENTION

```plsql
DBMS_STATS.GET_STATS_HISTORY_RETENTION 
RETURN VARCHAR;
```

此存储过程将获取统计信息历史收集记录保留时间（以天为单位）。

示例（单机/共享集群/分布式集群部署）

```plsql
SELECT dbms_stats.get_stats_history_retention FROM dual;
```

YashanDB（yashan模式）在SQL语法、表达式运算、FILTER CONDITION、数据类型、内置函数、系统视图和PL等基本功能上均与Oracle数据库兼容，数据库管理和开发人员不需要花费大量的时间去学习新知识，在已交付特性上直接查阅Oracle相关文档，也可流畅地操作使用YashanDB，实现从Oracle数据库到YashanDB的平滑迁移。 

在其他某些功能上YashanDB会与Oracle数据库的表现有所差异或暂时没有进行兼容，可能原因如下：

- YashanDB与Oracle数据库的底层架构、产品形态等并不相同。
- YashanDB摒弃了一些旧的不符合当前主流业务框架的方法，并增加了自己的特性。

本文将从如下方面具体说明，在单机部署和行式存储模式下，YashanDB对Oracle数据库的兼容情况：

- SQL语法
- 表达式运算
- FILTER CONDITION
- 数据类型
- 内置函数
- PL
- 系统视图
- 字符集
- SQL引擎
- 数据库安全
- 工具兼容
- 其他兼容

## SQL语法

YashanDB支持Oracle数据库中主流的SQL语法，其他少数因功能性缺失导致的不兼容将报语法不支持错误，此时应联系我们的技术支持提供变通方案，SQL语法详细说明请查阅[SQL语句](../../开发手册/SQL参考手册/SQL语句（yashan模式）/00SQL语句（yashan模式）)。

**（1）DML类**

**SELECT**

- 支持大部分查询功能，包括单、多表查询，子查询，内连接，半连接，外连接，分组及聚合，层次查询等
- 支持UNION、UNION ALL、INTERSECT、MINUS等集合操作
- 支持如下方式查看执行计划：
  - EXPLAIN
  - AUTOTRACE
- 支持随机抽样查询能力
- CTE支持递归功能

**INSERT**

- 支持单行、多行插入，同时支持指定分区插入
- 支持INSERT INTO SELECT语句
- 支持INSERT ALL语句
- 支持单表和多表插入

**UPDATE**

- 支持单列和多列的更新
- 支持使用子查询

**DELETE**

- 支持单表和多表的删除
- 支持使用子查询

**（2）DDL类**

YashanDB兼容Oracle数据库大部分的对象及对象管理操作，包括：

- HEAP表：
  - CREATE TABLE/CREATE TABLE AS
  - ALTER TABLE
  - DROP TABLE
  - TRUNCATE TABLE
- 临时表
  - GLOBAL TEMPORARY TABLE
  - PRIVATE TEMPORARY TABLE
- 分区表
  - 支持RANGE/INTERVAL/LIST/HASH类型分区
  - 支持ADD|DROP|TRUNCATE|SPLIT|MERGE PARTITION
  - 支持分区行迁移
  - 支持二级分区
- 外部表
  - 支持创建目录，CREATE DIRECTORY
  - 支持创建和删除外部表
- 约束
  - 包括in_line约束/out_of_line约束
  - 支持UNIQUE、PRIMARY KEY、FOREIGN KEY、CHECK、(NOT) NULL类型约束
- 视图
  - CREATE VIEW/CREATE OR REPLACE FORCE VIEW
  - DROP VIEW
  - 视图支持SELECT/INSERT/UPDATE/DELETE
- 物化视图
  - CREATE MATERIALIZED VIEW
  - ALTER MATERIALIZED VIEW
  - DROP MATERIALIZED VIEW
  - 支持本地物化视图，不支持远程物化视图
- BTree索引
  - 包括全局索引和本地（LOCAL）索引
  - 包括唯一（UNIQUE）索引和非唯一索引
  - 包含反向键索引和函数索引
  - 支持对索引REBUILD|UNUSABLE|COALESCE|PARALLEL|RENAME
  - 支持在相同列上创建多个索引
- 同义词
  - 包括私有同义词和公共（PUBLIC）同义词
- 序列
  - 包括升序序列和降序序列，可指定CYCLE|NOCYCLE、CACHE|NOCACHE、ORDER|NOORDER
  - 序列支持NEXTVAL和CURRVAL
- DBLINK
  - 支持Oracle到YashanDB、YashanDB到Oracle和YashanDB到YashanDB的远程连接
  - 支持PUBLIC和PRIVATE模式的LINK
  - 支持表和视图的连接
  - 支持在YashanDB创建远端对象的同义词、在YashanDB上调用远端存储过程和查看LOB数据

**（3）HINT**

YashanDB支持使用HINT并采用了Oracle的HINT语法，可实现join方式、join order、table scan、index scan等的指定能力。关于HINT的详细说明，请查阅[hint](../../开发手册/SQL参考手册/通用SQL语法/hint)。

## 表达式运算

YashanDB包含了主流的计算框架实现对表达式的运算，此外，Oracle本身由于没有布尔类型（只在其PL中支持）需要使用其他数据类型替代运算，YashanDB则实现了直接的布尔型表达式运算，详情如下表所示：

| 表达式运算类型 | YashanDB | Oracle数据库 |
| -------------- | -------- | ------------ |
| 二元运算加法   | 支持     | 支持         |
| 二元运算减法   | 支持     | 支持         |
| 二元运算乘法   | 支持     | 支持         |
| 二元运算除法   | 支持     | 支持         |
| 二元运算取余   | 支持     | 支持         |
| 一元运算取反   | 支持     | 支持         |
| 位运算与       | 支持     | 支持         |
| 位运算或       | 支持     | 支持         |
| 位运算异或     | 支持     | 支持         |
| Boolean运算    | 支持     | 不支持       |

## FILTER CONDITION

YashanDB的FILTER CONDITON类型完全兼容Oracle数据库的FILTER CONDITON类型，详情如下表所示：

| FILTER CONDITON | YashanDB | Oracle数据库 |
| --------------- | -------- | ------------ |
| ALL             | 支持     | 支持         |
| ANY             | 支持     | 支持         |
| AND             | 支持     | 支持         |
| EQUAL           | 支持     | 支持         |
| NOT EQUAL       | 支持     | 支持         |
| EXISTS          | 支持     | 支持         |
| NOT EXISTS      | 支持     | 支持         |
| GREAT EQUAL     | 支持     | 支持         |
| GREATE          | 支持     | 支持         |
| IN              | 支持     | 支持         |
| NOT IN          | 支持     | 支持         |
| IS NULL         | 支持     | 支持         |
| IS NOT NULL     | 支持     | 支持         |
| LESS            | 支持     | 支持         |
| LESS EQUAL      | 支持     | 支持         |
| LIKE            | 支持     | 支持         |
| NOT LIKE        | 支持     | 支持         |
| REG LIKE        | 支持     | 支持         |
| NOT REG LIKE    | 支持     | 支持         |
| OR              | 支持     | 支持         |
| SOME            | 支持     | 支持         |

## 数据类型

YashanDB目前包含了27种数据类型，与Oracle数据库对比情况如下表所示：

| 数据类型                       | YashanDB | Oracle数据库 |
| ------------------------------ | -------- | ------------ |
| BOOLEAN                        | 支持     | 不支持       |
| TINYINT                        | 支持     | 不支持       |
| SMALLINT                       | 支持     | 支持         |
| INTEGER                        | 支持     | 支持         |
| BIGINT                         | 支持     | 不支持       |
| FLOAT                          | 支持     | 支持         |
| DOUBLE                         | 支持     | 支持         |
| NUMBER                         | 支持     | 支持         |
| DATE                           | 支持     | 支持         |
| TIMESTAMP                      | 支持     | 支持         |
| TIMESTAMP WITH LOCAL TIME ZONE | 支持     | 支持         |
| TIMESTAMP WITH TIME ZONE       | 支持     | 支持         |
| TIME                           | 支持     | 不支持       |
| INTERVAL YEAR TO MONTH         | 支持     | 支持         |
| INTERVAL DAY TO SECOND         | 支持     | 支持         |
| CHAR                           | 支持     | 支持         |
| VARCHAR                        | 支持     | 支持         |
| NCHAR                          | 支持     | 支持         |
| NVARCHAR                       | 支持     | 支持         |
| ST_GEOMETRY                    | 支持     | 支持         |
| RAW                            | 支持     | 支持         |
| CLOB                           | 支持     | 支持         |
| NCLOB                          | 支持     | 支持         |
| BLOB                           | 支持     | 支持         |
| BIT                            | 支持     | 不支持       |
| ROWID                          | 支持     | 支持         |
| UROWID                         | 支持     | 支持         |
| CURSOR                         | 支持     | 支持         |
| JSON                           | 支持     | 支持         |
| XMLTYPE                        | 支持     | 支持         |


> **Note**：
>
> - YashanDB的大对象（CLOB/BLOB）数据类型有较强的存储能力，但大对象存取性能不推荐在复杂场景下使用。
>
> - YashanDB的XMLTYPE数据类型底层以CLOB进行存储，与Oracle存储方式不同，在函数调用时可能存在差异。

## 内置函数

YashanDB目前实现了超过120个内置函数，具体清单及说明请查阅[内置函数](../../开发手册/SQL参考手册/内置函数（yashan模式）/00内置函数（yashan模式）)。

与Oracle数据库的内置函数对比情况如下表所示：

| 函数类型     | 支持函数列表                                                 | YashanDB | Oracle数据库 |
| ------------ | ------------------------------------------------------------ | -------- | ------------ |
| 数学运算函数 | ABS、ACOS、ASIN、ATAN、ATAN2、COS、COT、 CEIL、DIV、EXP、FLOOR、LOG、LN、MOD、PI、POW、POWER、ROUND、SIGN、SIN、SINH、STDDEV、STDDEV_POP、STDDEV_SAMP、SQRT、TAN、TANH、TRUNC、VARIANCE、VAR_POP、VAR_SAMP、…… | 支持     | 支持         |
| 字符处理函数 | ASCII、CHR、CONCAT、INITCAP、INSTR、INSTRB、LEFT、LENGTH、LENGTH2、LOWER、LPAD、LTRIM、NLSSORT、POSITION、REPLACE、RIGHT、RPAD、RTRIM、SPLIT、STRPOS、SUBSTR、SUBSTRB、SOUNDEX、TRIM、TRANSLATE … USING、UNISTR、UPPER、empty_LOB、…… | 支持     | 支持         |
| 正则匹配函数 | REGEXP_LIKE、REGEXP_COUNT、REGEXP_INSTR、REGEXP_REPLACE、REGEXP_SUBSTR、…… | 支持     | 支持         |
| 转换函数     | BIN_TO_NUM、CAST、NUMTODSINTERVAL、NUMTOYMINTERVAL、ROWIDTOCHAR、TRANSLATE、TO_CHAR、TO_DATE、TO_DSINTERVAL、TO_NUMBER、TO_TIMESTAMP、TO_YMINTERVAL、…… | 支持     | 支持         |
| 集合处理函数 | COALESCE、DECODE、GREATEST、LEAST、NVL、NVL2、……             | 支持     | 支持         |
| 聚集函数     | AVG、COUNT、GROUP_CONCAT、LISTAGG、MAX、MIN、SUM、MEDIAN、PERCENTILE_CONT、…… | 支持     | 支持         |
| 窗口函数     | AVG、COUNT、FIRST、FIRST_VALUE、LAST、LAST_VALUE、LEAD、MAX、SUM、RANK、ROW_NUMBER、…… | 支持     | 支持         |
| 系统函数     | SCN_TO_TIMESTAMP、TIMESTAMP_TO_SCN、USERENV、……              | 支持     | 支持         |
| 时间处理函数 | ADD_MONTHS、CURRENT_TIMESTAMP、EXTRACT、LAST_DAY、NOW、NEXT_DAY、OVERLAPS、…… | 支持     | 支持         |
| 条件处理函数 | IF、IFNULL、ISNULL、NULLIF                                   | 支持     | 不支持       |
| JSON处理函数 | JSON、JSON_ARRAY_GET、JSON_ARRAY_LENGTH、JSON_EXISTS、JSON_FORMAT、JSON_PARSE、JSON_QUERY、JSON_SERIALIZE、JSON_VALUE、…… | 支持     | 支持         |
| 随机函数     | RANDOM                                                       | 支持     | 不支持       |
| 其他函数     | HEXTORAW、SQLCODE、SQLERRM、SYS_CONNECT_BY_PATH、……          | 支持     | 支持         |

## PL

YashanDB兼容了Oracle数据库大部分的PL功能，包括：

- 数据类型
- 流程控制
- 静态SQL
- 动态SQL
- 异常处理
  - 系统定义异常
  - 用户自定义异常
- 游标
  - BULK COLLECT
- 存储过程
  - 过程体加密wrap功能
- 触发器
  - 支持行级、语句级触发器。
  - 目前仅支持在表上创建触发器，不支持在视图上创建触发器
- 用户自定义高级包
  - 支持子过程
  - 支持全局变量和TYPE定义
  - PROCEDURE和FUNCTION支持重载
- 用户自定义函数
  - SQL语言的UDF
  - 外置JAVA语言UDF
  - 外置C语言的UDF
- 用户自定义数据类型
  - OBJECT TYPE
  - VARRAY TYPE
  - TABLE TYPE
  - 支持EXECUTE、UNDER ON对象级的权限控制
- JOB

## 内置高级包

YashanDB兼容了Oracle数据库部分的内置高级包，详情如下表所示：

| 高级包名称            |
| --------------------- |
| DBMS_AUDIT_MGMT       |
| DBMS_APPLICATION_INFO |
| DBMS_CRYPTO           |
| DBMS_DESCRIBE         |
| DBMS_HM               |
| DBMS_IJOB             |
| DBMS_JOB              |
| DBMS_LOB              |
| DBMS_LOCK             |
| DBMS_METADATA         |
| DBMS_MVIEW            |
| DBMS_OUTPUT           |
| DBMS_RANDOM           |
| DBMS_RESOURCE_MANAGER |
| DBMS_ROWID            |
| DBMS_SCHEDULER        |
| DBMS_SESSION          |
| DBMS_SQL              |
| DBMS_STANDARD         |
| DBMS_STATS            |
| DBMS_UTILITY          |
| DBMS_XA               |
| OWA_UTIL              |
| UTL_ENCODE            |
| UTL_FILE              |
| UTL_I18N              |
| UTL_RAW               |

## 系统视图

YashanDB兼容Oracle数据库的DBA视图清单如下：（ALL/USER视图兼容清单与DBA视图对应相同）

| 视图名称                       |
| ------------------------------ |
| AUDITABLE_SYSTEM_ACTIONS       |
| AUDIT_UNIFIED_ENABLED_POLICIES |
| AUDIT_UNIFIED_POLICIES         |
| COL                            |
| DICT_COLUMNS                   |
| DICTIONARY                     |
| DICT                           |
| DBA_ALL_TABLES                 |
| DBA_ARGUMENTS                  |
| DBA_AUDIT_MGMT_CLEANUP_JOBS    |
| DBA_AUDIT_MGMT_LAST_ARCH_TS    |
| DBA_COLL_TYPES                 |
| DBA_COL_COMMENTS               |
| DBA_CONSTRAINTS                |
| DBA_CONS_COLUMNS               |
| DBA_DATA_FILES                 |
| DBA_DB_LINKS                   |
| DBA_DEPENDENCIES               |
| DBA_EXTENTS                    |
| DBA_EXTERNAL_TABLES            |
| DBA_FREE_SPACE                 |
| DBA_HISTOGRAMS                 |
| DBA_INDEXES                    |
| DBA_IND_COLUMNS                |
| DBA_IND_EXPRESSIONS            |
| DBA_IND_PARTITIONS             |
| DBA_IND_STATISTICS             |
| DBA_IND_SUBPARTITIONS          |
| DBA_JOBS                       |
| DBA_LOBS                       |
| DBA_LOB_PARTITIONS             |
| DBA_LOB_SUBPARTITIONS          |
| DBA_LOG_GROUPS                 |
| DBA_MVIEWS                     |
| DBA_NESTED_TABLES              |
| DBA_OBJECTS                    |
| DBA_OUTLINES                   |
| DBA_OUTLINE_HINTS              |
| DBA_PART_COL_STATISTICS        |
| DBA_PART_HISTOGRAMS            |
| DBA_PART_INDEXES               |
| DBA_PART_KEY_COLUMNS           |
| DBA_PART_TABLES                |
| DBA_PROCEDURES                 |
| DBA_PROFILES                   |
| DBA_RECYCLEBIN                 |
| DBA_ROLES                      |
| DBA_ROLE_PRIVS                 |
| DBA_SCHEDULER_JOBS             |
| DBA_SEGMENTS                   |
| DBA_SEQUENCES                  |
| DBA_SOURCE                     |
| DBA_SUBPARTITION_TEMPLATES     |
| DBA_SUBPART_KEY_COLUMNS        |
| DBA_SYNONYMS                   |
| DBA_SYS_PRIVS                  |
| DBA_TABLES                     |
| DBA_TABLESPACES                |
| DBA_TAB_COLS                   |
| DBA_TAB_COLUMNS                |
| DBA_TAB_COL_STATISTICS         |
| DBA_TAB_COMMENTS               |
| DBA_TAB_HISTOGRAMS             |
| DBA_TAB_MODIFICATIONS          |
| DBA_TAB_PARTITIONS             |
| DBA_TAB_PRIVS                  |
| DBA_TAB_STATISTICS             |
| DBA_TAB_STAT_PREFS             |
| DBA_TAB_SUBPARTITIONS          |
| DBA_TEMP_FILES                 |
| DBA_TRIGGERS                   |
| DBA_TRIGGER_COLS               |
| DBA_TRIGGER_ORDERING           |
| DBA_TYPES                      |
| DBA_TYPE_ATTRS                 |
| DBA_TYPE_METHODS               |
| DBA_USERS                      |
| DBA_VIEWS                      |
| ROLE_SYS_PRIVS                 |
| ROLE_TAB_PRIVS                 |
| UNIFIED_AUDIT_TRAIL            |

YashanDB兼容Oracle数据库的动态视图清单如下：

| 视图名称                 |
| ------------------------ |
| V$2PC_PENDING            |
| V$ARCHIVE_DEST           |
| V$ARCHIVE_DEST_STATUS    |
| V$ARCHIVE_GAP            |
| V$ARCHIVED_LOG           |
| V$BUFFER_POOL            |
| V$BUFFER_POOL_STATISTICS |
| V$CONTROLFILE            |
| V$DATABASE               |
| V$DATAFILE               |
| V$DIAG_INCIDENT          |
| V$DIAG_PROBLEM           |
| V$EVENT_HISTOGRAM        |
| V$EVENT_NAME             |
| V$FIXED_TABLE            |
| V$FIXED_VIEW_DEFINITION  |
| V$HM_CHECK               |
| V$HM_CHECK_PARAM         |
| V$HM_FINDING             |
| V$HM_RUN                 |
| V$INSTANCE               |
| V$LOCK                   |
| V$LOCKED_OBJECT          |
| V$LOGFILE                |
| V$MYSTAT                 |
| V$OPEN_CURSOR            |
| V$OSSTAT                 |
| V$PARAMETER              |
| V$PQ_TQSTAT              |
| V$PROCESS                |
| V$PX_SESSION             |
| V$RECOVERY_PROGRESS      |
| V$RESERVED_WORDS         |
| V$ROLLBACK               |
| V$SEGMENT_STATISTICS     |
| V$SEGSTAT                |
| V$SESS_TIME_MODEL        |
| V$SESSION                |
| V$SESSION_EVENT          |
| V$SESSION_WAIT           |
| V$SESSTAT                |
| V$SGA                    |
| V$SGASTAT                |
| V$SQL                    |
| V$SQL_BIND_CAPTURE       |
| V$SQL_PLAN               |
| V$SQL_PLAN_STATISTICS    |
| V$SQLAREA                |
| V$SQLSTATS               |
| V$SQLTEXT                |
| V$STATNAME               |
| V$SYSSTAT                |
| V$SYSTEM_EVENT           |
| V$SYSTEM_PARAMETER       |
| V$SYSTEM_WAIT_CLASS      |
| V$TABLESPACE             |
| V$TEMP_EXTENT_POOL       |
| V$TEMPSEG_USAGE          |
| V$TRANSACTION            |
| V$UNDOSTAT               |
| V$VERSION                |

## 字符集

YashanDB支持如下字符集：

- ASCII
- GBK
- UTF8
- ISO88591
- GB18030

同时，YashanDB支持如下字符集排序方式：

- ASCII_GENERAL_CS
- ASCII_GENERAL_CI
- GBK_GENERAL_CS
- GBK_GENERAL_CI
- UTF8_GENERAL_CS
- UTF8_GENERAL_CI
- UTF8_PINYIN_CS
- UTF8_PINYIN_CI
- ISO88591_GENERAL_CS
- ISO88591_GENERAL_CI
- GB18030_GENERAL_CS
- GB18030_GENERAL_CI
- GB18030_PINYIN_CS
- GB18030_PINYIN_CI

## SQL引擎

YashanDB的SQL引擎兼容了Oracle数据库大部分的特性，包括：

- 支持查询改写
- 支持预编译语句
- 支持基于成本的优化器
- 支持执行计划生成与展示（EXPLAIN）
- 支持执行计划缓存
- 支持执行计划快速参数化
- 支持执行计划绑定
- 支持Optimizer Hint
- 支持OutLine
- 支持like和reglike的模糊匹配能力

## 数据库安全

**权限管理**

- 兼容Oracle数据库的系统级权限
- 兼容常见的对象权限，支持table、view对象级权限管理及使用
- 支持授权与移除权限
  - with admin option
  - with grant option
- 支持创建用户自定义角色
- 支持DBA和PUBLIC系统预定义角色及其权限认证
- 提供视图查询权限相关内容

**身份鉴别**

- 兼容Oracle数据库的密码策略
- profile支持用户密码管理

**审计**

- 兼容Oracle数据库的统一审计

## 工具兼容

**imp**

- 支持FULL、FROM/TOUSER和TABLES模式的维度导入
- 支持指定元数据和数据导入
- 支持覆盖已存在的表，即truncate模式

**exp**

- 支持FULL、OWNER和TABLES模式的维度导出
- 支持指定元数据和数据导出

**运维工具**

- 支持10053跟踪事件

## 其他兼容

**统计信息**

YashanDB的统计信息体系兼容了Oracle数据库大部分的特性，包括：

- 支持收集表（分区表）、索引、列等维度统计信息
- 支持动态采样功能
- 支持锁定统计信息
- 支持收集列直方图统计信息
- 支持通过JOB配置收集统计信息任务
- 支持使用高级包DBMS_STATS导入导出统计信息
- 支持基础统计信息的实时收集

**表空间**

- 支持ONLINE/OFFLINE
- 支持RENAME

**可靠性**

- 支持全库闪回、闪回DML操作、闪回查询功能
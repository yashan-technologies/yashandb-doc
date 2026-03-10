## 通用描述

FLASHBACK用于实现对数据库或表的历史数据闪回。

## 语句定义

**flashback::=**

```ebnf+diagram
syntax::= FLASHBACK (database_clauses|table_clauses)
```

**[database\_clauses](#databaseclauses)::=**

```ebnf+diagram
syntax::= DATABASE (TO (SCN scn|TIME timestamp|RESTORE POINT restore_point_name))
```

**[table\_clauses](#tableclauses)::=**

```ebnf+diagram
syntax::= TABLE table_name (TO (SCN scn|TIMESTAMP timestamp| BEFORE (DROP [RENAME TO new_name]|TRUNCATE)) | (PARITITION|SUBPARTITION) part_name TO BEFORE TRUNCATE)
```

<span id="databaseclauses" name="databaseclauses" class="yaslink"></span>

### 1. database\_clauses

该语句用于对数据库进行全库闪回。

该语句不适用于存算一体分布式集群部署。

FLASHBACK DATABASE需遵守如下规则：

- 确保已开启[全库闪回](ALTER DATABASE.html#flashbackdatabaseclauses)功能。
- 目前无法闪回文件类DDL（例如表空间DDL、数据文件DDL等）、NOLOGGING对象。
- 归档日志文件和redo日志文件的操作不会被闪回。
- LSC表冷数据物理文件闪回后不会被清理，需要手动删除。
- 如需闪回至指定还原点，需先[CREATE RESTORE POINT](CREATE RESTORE POINT)。
- 执行全库闪回时数据库需处于MOUNT状态，闪回完成后必须[ALTER DATABASE OPEN RESETLOGS](ALTER DATABASE.html#startupclauses)重置redo时间线。

#### 1.1. TO SCN|TIME

该语句用于将数据库闪回至指定的SCN|TIME点。

示例（单机/共享集群/分布式集群部署）

```sql
-- 查询V$FLASHBACK_DATABASE_LOG获取能闪回的最老时间点
SELECT OLDEST_FLASHBACK_SCN,OLDEST_FLASHBACK_TIME FROM V$FLASHBACK_DATABASE_LOG;

-- 根据获取出来的最老时间点决定闪回的时间点
FLASHBACK DATABASE TO SCN 625007001600000000;
FLASHBACK DATABASE TO TIME TIMESTAMP;
```

#### 1.2. TO RESTORE POINT

该语句用于将数据库闪回至指定的还原点，可通过V$RESTORE_POINT视图获取已有还原点信息。

示例（单机/共享集群/分布式集群部署）

```sql
-- 查询V$RESTORE_POINT获取当前所有的还原点信息
SELECT * FROM V$RESTORE_POINT;

-- 根据视图中的还原点信息决定需要闪回至哪个还原点
FLASHBACK DATABASE TO RESTORE POINT p202412011100;
```

<span id="tableclauses" name="tableclauses" class="yaslink"></span>

### 2. table\_clauses

该语句用于对表的历史数据进行闪回。

FLASHBACK TABLE需遵守如下规则：

- 仅适用于HEAP表。
- FLASHBACK TABLE TO SCN不允许使用于指定SCN点至今发生过以下改变表结构的DDL的场景：
    - UPDATE TABLE, MOVE TABLE, TRUNCATE TABLE
    - 表增加约束
    - MODIFY COLUMN, DROP COLUMN
    - ADD、DROP、MERGE、SPLIT、COALESCE、TRUNCATE分区或二级分区
- FLASHBACK不允许在事务中进行。
- FLASHBACK的目标表不允许与其他表存在依赖关系。
- FLASHBACK不维护ROWID。
- FLASHBACK仅对当前时刻存在的索引进行维护：
    - SCN点尚未创建但当前时刻存在的索引：该类索引的数据将被闪回，但索引的元属性会以当前时刻为准
    - SCN点存在但当前时刻已不存在的索引：不会恢复该类索引及其数据
- FLASHBACK不允许会违反约束的数据。
- FLACKBACK应用于多表的场景下必须全部成功或全部失败。
- FLACKBACK不还原统计信息。

#### 2.1. table\_name

指定待闪回恢复的表名称，可以为：

- 原表名（[DBA_RECYCLEBIN](../../../参考手册/系统视图/DBA视图/DBA_RECYCLEBIN)视图的ORIGINAL_NAME字段）

- 目标表进入回收站时系统生成的BIN开头对象名（[DBA_RECYCLEBIN](../../../参考手册/系统视图/DBA视图/DBA_RECYCLEBIN)视图的OBJECT_NAME字段）

#### 2.2. TO SCN|TIMESTAMP

该语句用于将表的数据闪回至指定的SCN|TIMESTAMP点，此功能无需开启回收站。

通过此功能闪回的数据可能会发生ROWID的变化，因此执行前需要开启表的ROW MOVEMENT开关（参考[ALTER TABLE](./ALTER TABLE)中描述语法），否则将返回错误。

若需要闪回的表存在外键约束，则需要先删除其他表中的外键约束再执行闪回，否则将返回错误。

TO SCN|TIMESTAMP闪回不适用于二级分区，即无法闪回二级分区至指定的SCN|TIMESTAMP点。

示例（HEAP表）

```sql
-- 开启finance_info的ROW MOVEMENT开关
ALTER TABLE finance_info ENABLE ROW MOVEMENT;

-- finance_info表中存在的一条记录
SELECT year,month,branch,revenue_total,cost_total,fee_total FROM finance_info WHERE year='2021' AND month='02';
YEAR  MONTH BRANCH REVENUE_TOTAL  COST_TOTAL   FEE_TOTAL
----- ----- ------ ------------- ----------- -----------
2021  02    0101           37778       33000        6000   
 
-- 获取当前时间戳
SELECT SYSTIMESTAMP res FROM dual;
RES                                           
-----------------------------------------------
2023-12-17 14:10:28.736908   

-- 删除此条记录并提交
DELETE FROM finance_info WHERE year='2021' AND month='02';
COMMIT;
SELECT year,month,branch,revenue_total,cost_total,fee_total FROM finance_info WHERE year='2021' AND month='02';
YEAR  MONTH BRANCH REVENUE_TOTAL  COST_TOTAL   FEE_TOTAL
----- ----- ------ ------------- ----------- -----------

-- 利用FLASHBACK闪回历史数据（通过时间戳闪回）
FLASHBACK TABLE finance_info TO TIMESTAMP  TIMESTAMP('2023-12-17 14:10:28.736908');
SELECT year,month,branch,revenue_total,cost_total,fee_total FROM finance_info WHERE year='2021' AND month='02';
YEAR  MONTH BRANCH REVENUE_TOTAL  COST_TOTAL   FEE_TOTAL
----- ----- ------ ------------- ----------- -----------
2021  02    0101           37778       33000        6000   

-- 获取最后一次修改时SCN（通过ROWSCN获取）
SELECT rowscn FROM finance_info WHERE year='2021' AND month='02';
               ROWSCN 
--------------------- 
   408883147271815168

-- 再次删除记录
DELETE FROM finance_info WHERE year='2021' AND month='02';
COMMIT;
SELECT year,month,branch,revenue_total,cost_total,fee_total FROM finance_info WHERE year='2021' AND month='02';
YEAR  MONTH BRANCH REVENUE_TOTAL  COST_TOTAL   FEE_TOTAL
----- ----- ------ ------------- ----------- -----------

-- FLASHBACK闪回历史数据（通过SCN闪回）
FLASHBACK TABLE finance_info TO SCN 408883147271815168;
SELECT year,month,branch,revenue_total,cost_total,fee_total FROM finance_info WHERE year='2021' AND month='02';
YEAR  MONTH BRANCH REVENUE_TOTAL  COST_TOTAL   FEE_TOTAL
----- ----- ------ ------------- ----------- -----------
2021  02    0101           37778       33000        6000  
```

#### 2.3. BEFORE

该语句用于将被删除的表闪回至删表之前的状态，或者将被TRUNCATE的表的数据闪回，此功能需要开启回收站（[修改配置参数](./ALTER SYSTEM)RECYCLEBIN_ENABLED为ON）。

##### 2.3.1. DROP

该语句用于闪回被删除的表，包括表结构和数据。

实际情况中，被删除的表名称可能已经被重建，或被其他对象占用，此时需指定RENAME TO关键字用于为恢复的表重命名，否则将报YAS-02013错误。

示例（HEAP表）

```sql
ALTER SYSTEM SET RECYCLEBIN_ENABLED=ON;

DROP TABLE finance_info;

SELECT year,month,branch,revenue_total,cost_total,fee_total FROM finance_info;
[1:15]YAS-02012 table or view does not exist

FLASHBACK TABLE finance_info TO BEFORE DROP;
-- 若finance_info名称已被使用，则需对其重命名
-- FLASHBACK TABLE finance_info TO BEFORE DROP RENAME TO finance_info_new;

SELECT year,month,branch,revenue_total,cost_total,fee_total FROM finance_info;
YEAR  MONTH BRANCH REVENUE_TOTAL  COST_TOTAL   FEE_TOTAL 
----- ----- ------ ------------- ----------- ----------- 
2001  01    0201            2888        2000         300
2021  01    0201           28888       24000        3000
2021  01    0101           38888       34000        4000
2021  02    0101           37778       33000        6000
```

##### 2.3.2. TRUNCATE

该语句用于闪回被TRUNCATE过的表的数据。不论是否存在多次TRUNCATE，均只能闪回最后一次TRUNCATE的数据。

示例（HEAP表）

```sql
ALTER SYSTEM SET RECYCLEBIN_ENABLED=ON;

TRUNCATE TABLE sales_info_range;

SELECT year,month,branch,product,quantity,amount,salsperson FROM sales_info_range;
YEAR  MONTH BRANCH PRODUCT      QUANTITY      AMOUNT SALSPERSON    
----- ----- ------ --------- ----------- ----------- ------------- 

FLASHBACK TABLE sales_info_range TO BEFORE TRUNCATE;

SELECT year,month,branch,product,quantity,amount,salsperson FROM sales_info_range;
YEAR  MONTH BRANCH PRODUCT      QUANTITY      AMOUNT SALSPERSON    
----- ----- ------ --------- ----------- ----------- ------------- 
2001  01    0201   11001              30         500 0201010011   
2000  12    0102   11001              20         300              
2015  11    0101   11001              20         300              
2015  03    0102   11001              20         300              
2021  10    0101   11001              20         300              
2021  05    0101   11001              40         600  
```

#### 2.4. PARTITION|SUBPARTITION

该语句用于闪回被TRUNCATE过的某个分区或二级分区的数据，此功能需要开启回收站（[修改配置参数](./ALTER SYSTEM)RECYCLEBIN_ENABLED为ON）。空的分区在TRUNCATE时也会进入回收站，也可以被闪回。

在实际应用中，某个分区或二级分区被TRUNCATE后可能会插入新数据，此时再执行闪回将导致这部分“新数据”被TRUNCATE到回收站中。同时若有存在该表被外键依赖的全局索引主键或唯一键则会报错。

直接TRUNCATE整个分区表后，无法单独闪回该表的某个分区。同理，TRUNCATE一级分区后也无法单独闪回该一级分区的某个二级分区。

某个分区或二级分区被DROP后，将无法再被闪回。

示例（HEAP表）

```sql
ALTER SYSTEM SET RECYCLEBIN_ENABLED=ON;

ALTER TABLE sales_info TRUNCATE PARTITION p_sales_info_1;
ALTER TABLE sales_info TRUNCATE SUBPARTITION  P_SALES_INFO_2_SP_SALES_INFO_1;

SELECT year,month,branch,product,quantity,amount,salsperson FROM sales_info PARTITION (p_sales_info_1);
YEAR  MONTH BRANCH PRODUCT      QUANTITY      AMOUNT SALSPERSON    
----- ----- ------ --------- ----------- ----------- ------------- 

INSERT INTO sales_info VALUES ('2021','06','0402','11001',40,600,'');
COMMIT;

FLASHBACK TABLE sales_info PARTITION p_sales_info_1 TO BEFORE TRUNCATE;
FLASHBACK TABLE sales_info SUBPARTITION P_SALES_INFO_2_SP_SALES_INFO_1 TO BEFORE TRUNCATE;
-- 闪回后上面新增的数据被放入回收站，p_sales_info_list_2分区中只存在回收站上一次的数据
SELECT year,month,branch,product,quantity,amount,salsperson FROM sales_info PARTITION (p_sales_info_1);
YEAR  MONTH BRANCH PRODUCT      QUANTITY      AMOUNT SALSPERSON
----- ----- ------ --------- ----------- ----------- -------------
2021  10    0402   11001              20         300
```

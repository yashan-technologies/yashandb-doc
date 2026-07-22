数据闪回是指通过UNDO日志查找现有表的过去某一时刻的历史数据，并作为闪回查询的结果集返回或作为FLASHBACK TABLE TO SCN|TIMESTAMP恢复的目标状态。

数据闪回的时间跨度为UNDO日志的生命周期（即撤销保持期），影响因素主要包括：

- 撤销保持期

   由配置参数UNDO_RETENTION的取值控制，默认为300秒。理论上UNDO BLOCK从事务提交那一刻开始将会保留UNDO_RETENTION指定时长，超过规定时间后UNDO BLOCK可被随意复用。

- UNDO表空间容量

   如果UNDO表空间资源不足无法分配任何BLOCK时，也可能会复用已提交事务的未超过保留时间的BLOCK。

因此，DBA应结合实际业务对数据闪回时长的需求、数据库磁盘配置等各方面综合考虑，合理进行[UNDO表空间](../../存储管理/逻辑空间管理/表空间管理/UNDO表空间管理)相关配置的规划。

## 查询现有表的历史数据

闪回查询常用于数据审计、错误溯源、找寻其他操作（例如恢复）的目标时间点等场景，例如在发现某些数据不符合预期时，可通过闪回查询回溯历史数据，帮助判断定位错误的根源。

语法详情请查阅SELECT的[flashback_query_clause](../../../开发手册/SQL参考手册/SQL语句/SELECT.md#flashbackqueryclause)。

示例（单机、共享集群/分布式集群部署）

```sql
-- 查看目标表的当前数据，以area表为例
SELECT area_no,area_name,DHQ FROM area;
AREA_NO   AREA_NAME                                                        DHQ
--------- ---------------------------------------------------------------- -----------------------------------------
01        EastChina                                                        Shanghai
02        WestChina                                                        Chengdu
03        SouthChina                                                       Guangzhou
04        NorthChina                                                       Beijing
05        CentralChina                                                     Wuhan      
 
-- 获取当前时间
SELECT SYSTIMESTAMP res FROM dual;
RES                                            
----------------------------------------------------------------
2023-12-17 14:14:08.498126     

-- 获取当前最新SCN
SELECT CURRENT_SCN FROM V$DATABASE;

          CURRENT_SCN
---------------------
   725539138356854784
 
-- 删除此条记录并提交
DELETE FROM area WHERE area_no='03';
COMMIT;
SELECT area_no,area_name,DHQ FROM area WHERE area_no='03';
AREA_NO AREA_NAME          DHQ         
------- ------------------ ------------
 
-- 闪回查询历史数据
-- 方式1：基于TIMESTAMP
SELECT area_no,area_name,DHQ FROM area AS OF TIMESTAMP TIMESTAMP('2023-12-17 14:14:08.498126') WHERE area_no='03';
AREA_NO AREA_NAME          DHQ         
------- ------------------ ------------
03      SouthChina             Guangzhou     

-- 方式2：基于SCN
SELECT area_no,area_name,DHQ FROM area AS OF SCN 725539138356854784 WHERE area_no='03';
AREA_NO AREA_NAME          DHQ         
------- ------------------ ------------
03      SouthChina             Guangzhou     
```

## 恢复现有表的数据

闪回恢复现有表的数据（即FLASHBACK TABLE TO SCN|TIMESTAMP）是指将某个表的数据恢复至过去某一时刻的状态，撤销在此期间对该表执行过的INSERT、DELETE等操作。

FLASHBACK TABLE TO SCN|TIMESTAMP的使用约束如下：

- 仅适用于HEAP表。

- 不允许在事务中执行。

- 目标表不允许与其他表存在依赖关系。若目标表存在外键约束，需先删除其他表中的外键约束才能闪回目标表。

- 在当前时间点至目标闪回时间点期间，目标表的结构未发生变化，即未执行过以下DDL：
   
   - 表增加约束
   
   - MODIFY/DROP COLUMN

   - ADD/DROP/MERGE/SPLIT/COALESCE/TRUNCATE分区或二级分区

   - TRUNCATE TABLE

- 仅对当前时刻存在的索引进行维护：

   - 目标历史时刻尚未创建但当前时刻存在的索引：该类索引的数据将被闪回，但索引的元属性会以当前时刻为准。

   - 目标历史时刻存在但当前时刻已不存在的索引：不会恢复该类索引及其数据。

- 不维护目标表的ROWID，闪回后的数据可能会改变ROWID，请确保应用程序不依赖于目标表的ROWID。

- 同时闪回多个表时，一旦某个表无法成功闪回，所有表全部都不闪回。

- 闪回不会直接还原目标表相关的统计信息，统计信息只取决于收集时的现状。

### 前提条件

- 执行表闪回操作需使用具备DBA权限或FLASHBACK相关权限的用户。

- 目标表已开启ROW MOVEMENT。

### 操作示例

1. 以DBA用户连接并登录数据库。
    ```shell
    $ yasql sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```


2. 开启目标表的ROW MOVEMENT开关，本文以finance_info表为例。

   ```sql
   ALTER TABLE finance_info ENABLE ROW MOVEMENT;
   ```

3. 查看目标表的当前数据以及当前时间戳。

   ```sql
   -- 查看目标表的当前数据
   SELECT year,month,branch,revenue_total,cost_total,fee_total FROM finance_info;

   YEAR      MONTH     BRANCH    REVENUE_TOTAL  COST_TOTAL   FEE_TOTAL
   --------- --------- --------- ------------- ----------- -----------
   2001      01        0201               2888        2000         300
   2021      01        0201              28888       24000        3000
   2021      01        0101              38888       34000        4000
   2021      02        0101              37778       33000        6000

   -- 获取当前时间戳
   SELECT SYSTIMESTAMP res FROM dual;

   RES                                           
   -----------------------------------------------
   2023-12-17 14:10:28.736908   

   -- 获取当前最新SCN
   SELECT CURRENT_SCN FROM V$DATABASE;

             CURRENT_SCN
   ---------------------
      725955893872099328
   ```

4. 删除目标表的某条数据并提交事务。

   ```sql
   -- 删除数据
   DELETE FROM finance_info WHERE year='2021' and month='02';
   COMMIT;

   -- 确认删除结果
   SELECT year,month,branch,revenue_total,cost_total,fee_total FROM finance_info WHERE year='2021' and month='02';
   YEAR  MONTH BRANCH REVENUE_TOTAL  COST_TOTAL   FEE_TOTAL
   ----- ----- ------ ------------- ----------- -----------
   ```

5. 闪回目标表的数据，可以基于TIMESTAMP或SCN闪回，择一即可。

   ```sql
   -- 方式1：基于TIMESTAMP闪回
   FLASHBACK TABLE finance_info TO TIMESTAMP TIMESTAMP('2023-12-17 14:10:28.736908');

   -- 方式2：基于SCN闪回
   FLASHBACK TABLE finance_info TO SCN 725955893872099328;

   -- 确认闪回结果
   SELECT year,month,branch,revenue_total,cost_total,fee_total FROM finance_info WHERE year='2021' and month='02';
   YEAR  MONTH BRANCH REVENUE_TOTAL  COST_TOTAL   FEE_TOTAL
   ----- ----- ------ ------------- ----------- -----------
   2021  02    0101           37778       33000        6000   
   ```

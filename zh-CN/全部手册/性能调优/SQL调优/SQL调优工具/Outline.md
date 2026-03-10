Outline通过固定SQL语句执行计划确保执行计划稳定性。使用[CREATE OUTLINE](../../../开发手册/SQL参考手册/SQL语句/CREATE OUTLINE)创建指定存储纲要，SQL_TEXT语法会根据SQL语句对应的当前执行计划生成对应的Hint信息，SQL_ID语法为具体某条SQL语句设置Hint信息。当存储纲要可用时，数据库将自动根据这些Hint信息生成对应SQL语句的执行计划。

## Outline开关

系统通过USE_STORED_OUTLINES（默认值`FALSE`）控制Outline开关，对该参数的值说明如下：

- true：使用Category为DEFAULT的Outline。
- false：默认值，不使用Outline。
- category_name ：使用指定Category的Outline。

**Category**

在创建OUTLINE时，需要为其指定一个类别，用于将Outline分类管理和控制。

SQL_ID语法创建的OUTLINE没有CATEGORY，USE_STORED_OUTLINES配置非false时即可以使用。

## Outline管理

Outline由用户来定义和维护，包括创建、修改和删除，具体操作请参考[CREATE OUTLINE](../../../开发手册/SQL参考手册/SQL语句/CREATE OUTLINE)、[ALTER OUTLINE](../../../开发手册/SQL参考手册/SQL语句/ALTER OUTLINE)、[DROP OUTLINE](../../../开发手册/SQL参考手册/SQL语句/DROP OUTLINE)。

成功创建的Outline可通过DBA_OUTLINES/ALL_OUTLINES/USER_OUTLINES视图查看其定义信息，通过DBA_OUTLINE_HINTS/ALL_OUTLINE_HINTS/USER_OUTLINE_HINTS视图查看其包含的Hint。

## Outline限制

当前outline只支持生成SCAN类与JOIN类的hint，即[hint](../../../开发手册/SQL参考手册/通用SQL语法/hint) 中的 FULL，INDEX，USE_NL，USE_MERGE，USE_HASH这几类。

## Outline使用

1. 创建一个Outline：

   ```sql
   -- 对给定的SQL创建Outline。
   CREATE OUTLINE yashan_outline FOR CATEGORY ctgy_yashan ON 
   SELECT /*+ FULL(a) */ a.area_name, b.branch_name
   FROM area a, branches b
   WHERE a.area_no = b.area_no
   AND b.branch_no LIKE '01%'
   AND a.area_no LIKE '01';

   CREATE OUTLINE yashan_outline1 FOR CATEGORY ctgy_yashan ON 
   SELECT /*+ FULL(a) */ a.area_name
   FROM area a
   WHERE a.area_no LIKE '01';
   ```

2. 开启ctgy_yashan类别的Outline，该类别下所有的Outline都将会生效并被优化器识别：

   ```sql
   ALTER SESSION SET USE_STORED_OUTLINES=ctgy_yashan;
   ```

3. 查看Outline是否生效，请注意系统将首先对此SQL语句和ctgy_yashan类别下的SQL语句进行大小写不敏感匹配，匹配成功的情况下，优化器才会采纳该Outline并在执行计划中进行列示，如下：

   ```sql
   EXPLAIN 
   SELECT /*+ FULL(a) */ a.area_name, b.branch_name
   FROM area a, branches b
   WHERE a.area_no = b.area_no
   AND b.branch_no LIKE '01%'
   AND a.area_no LIKE '01';

   PLAN_DESCRIPTION                                                 
   ---------------------------------------------------------------- 
   SQL hash value: 1136128624                                      
   Optimizer: ADOPT_C                                              
                                                                  
   +----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
   | Id | Operation type                 | Name                 | Owner      | Rows     | Cost(%CPU)  | Partition info                 |
   +----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
   |  0 | SELECT STATEMENT               |                      |            |          |             |                                |
   |* 1 |  HASH JOIN INNER               |                      |            |      8000|      722( 0)|                                |
   |* 2 |   TABLE ACCESS FULL            | AREA                 | SALES      |     10000|      445( 0)|                                |
   |* 3 |   TABLE ACCESS BY INDEX ROWID  | BRANCHES             | SALES      |          |             |                                |
   |* 4 |    INDEX RANGE SCAN            | SYS_C_36             | SALES      |     10000|      159( 0)|                                |
   +----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
                                                                  
   Operation Information (identified by operation id):             
   ---------------------------------------------------             
                                                                  
      1 - Predicate : access("A"."AREA_NO" = "B"."AREA_NO")        
      2 - Predicate : filter("A"."AREA_NO" LIKE '01')              
      3 - Predicate : filter("B"."BRANCH_NO" LIKE '01%')           
      4 - Predicate : access("B"."BRANCH_NO" LIKE '01%')           
                                                                  
   Hint Information :                                              
   ---------------------------------------------------             
                                                                  
   full(a) / rejected by IGNORE_OPTIM_EMBEDDED_HINTS               
                                                                  
   Outline Information :                                           
   ---------------------------------------------------             
                                                                  
      - outline OL_AB used for this statement 
   ```

4. 查看视图，确认Outline包含的hint是否与执行计划中所列算子和顺序一致：

   ```sql
   SELECT join_pos, hint
   FROM USER_OUTLINE_HINTS
   WHERE name = 'OL_AB';
   JOIN_POS HINT                                                             
   -------- -----------------------
         0 LEADING(A B)                                                    
         0 USE_HASH(A B)                                                   
         1 FULL(A)                                                         
         1 INDEX(B SYS_C_36) 
   ```

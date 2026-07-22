通用描述
----

SELECT用于执行对数据库的表、视图、AC等的查询操作。

YashanDB支持丰富多样的查询操作，包括但不限于：

- 多表连接（[子查询](#subquery)、[SET](#setoper)、[JOIN](#joinclause)等）
- 排序[ORDER BY](#orderbyclause)
- 分组[GROUP BY](#groupbyclause)
- [CASE](#caseclause)
- [LIMIT](#rowlimitingclause)
- [CTE](#cteclause)
- [层次化/递归](#hierarchicalqueryclause)
- [抽样](#sampleclause)
- [指定分区](#queryname)
- [指定切片](#queryslice)（Slice）
- [闪回查询](#flashbackqueryclause)

其中，对AC可以执行上述列示的多表连接、排序、分组等基本查询。

在存算一体分布式集群部署中，[全局动态视图](../../../参考手册/系统视图/动态视图/00动态视图.md)相关的操作均在各节点本地执行，最后再汇总至CN。

语句定义
----

**select::=**

```ebnf
= subquery [for_update_clause].
```

**[subquery](#subquery)::=**

```ebnf
= (query_block
|(subquery set_oper subquery {" " set_oper subquery})
|"(" subquery ")")
[order_by_clause]
[row_limiting_clause|offset_fetch_clause].
```

**[query\_block](#queryblock)::=**

```ebnf
= [with_clause]
SELECT [hint] [DISTINCT] select_list
FROM (table_reference|join_clause|"(" join_clause ")")
{" " (table_reference|join_clause|"(" join_clause ")")}
[where_clause]
[hierarchical_query_clause]
[group_by_clause].
```

**[with\_clause](#withclause)::=**

```ebnf
= WITH cte_clause {"," cte_clause}.
```

**[cte\_clause](#cteclause)::=**

```ebnf
= cte_name ["(" column_alias {"," column_alias} ")"] AS "(" subquery ")" .
```

**[select\_list](#selectlist)::=**

```ebnf
= ("*"|expr_clause [[AS] alias]) {"," ("*"|expr_clause [[AS] alias])} .
```

**[expr\_clause](#exprclause) ::=**

```ebnf
= ["("] (query_block|expr|case_clause|udt_expr) [")"].
```

**[case\_clause](#caseclause)::=**

```ebnf
= CASE (simple_case_expression|searched_case_expression)
{" " (simple_case_expression|searched_case_expression)}
[else_clause] END.
```

**[simple\_case\_expression](#SimpleCase)::=**

```ebnf
= expr (WHEN comparison_expr THEN return_expr)
{" " (WHEN comparison_expr THEN return_expr)}.
```

**[searched\_case\_expression](#SimpleCase)::=**

```ebnf
= (WHEN condition THEN return_expr) {" " (WHEN condition THEN return_expr)}.
```

**[else\_clause](#SimpleCase)::=**

```ebnf
= ELSE else_expr.
```

**[table\_reference](#tablereference)::=**

```ebnf
= ((query_name|query_containers|subquery)[sample_clause][flashback_query_clause][t_alias][pivot_clause|unpivot_clause]) {"," ((query_name|query_containers|subquery) [sample_clause][flashback_query_clause][t_alias][pivot_clause|unpivot_clause])}.
```

**[query_name](#queryname) ::=**

```ebnf
= [schema "."]  (((table_name[dblink]|synonym_name)[partition_extension_clause][SLICE "(" slice_id ")" ]  )
|view_name
|table_collection_expression)
[sample_clause]
[pivot_clause|unpivot_clause].
```

**[partition\_extension\_clause](#partitionextensionclause)::=**

```ebnf
= PARTITION ("(" partition ")"|FOR "(" partition_key_value {","partition_key_value} ")")|SUBPARTITION ("(" subpartition ")"|FOR "(" subpartition_key_value {","subpartition_key_value} ")").
```

**[table_collection_expression](#tablecollectionexpression)::=**

```ebnf
= TABLE "(" collection_expression ")".
```

**[sample_clause](#sampleclause)::=**

```ebnf
= SAMPLE "(" sample_percent ")" [SEED "(" seed_value ")"].
```

**[query_containers](#querycontainers)::=**

```ebnf
= CONTAINERS '(' ([schema'.'] query_name) ')'.
```

**[flashback_query_clause](#flashbackqueryclause)::=**

```ebnf
= AS OF ((SCN|TIMESTAMP) expr) .
```

**[pivot\_clause](#pivotclause)::=**

```ebnf
= PIVOT "(" aggregate_expression [[AS] alias] { "," aggregate_expression [[AS] alias] } FOR column_expression IN "(" const_expression [[AS] alias] { "," const_expression [[AS] alias] }  ")" ")" .
```

**[unpivot\_clause](#unpivotclause)::=**

```ebnf
= UNPIVOT[( INCLUDE | EXCLUDE) NULLS ] "(" value_column FOR pivot_column IN "(" column [AS alias ] { "," column [AS alias] } ")" ")" .
```

**[join\_clause](#joinclause)::=**

```ebnf
= table_reference (inner_cross_join_clause|outer_join_clause )
{" " (inner_cross_join_clause|outer_join_clause)}.
```

**[inner\_cross\_join\_clause](#innercrossjoinclause)::=**

```ebnf
= ([INNER] JOIN table_reference ON condition)|(CROSS JOIN table_reference).
```

**[outer\_join\_clause](#outerjoinclause)::=**

```ebnf
= outer_join_type JOIN table_reference [ON condition].
```

**[outer\_join\_type](#outerjointype)::=**

```ebnf
= (LEFT|RIGHT|FULL) [OUTER].
```

**[where\_clause](#whereclause)::=**

```ebnf
= WHERE condition.
```

**[hierarchical\_query\_clause](#hierarchicalqueryclause)::=**

```ebnf
= ((([connect_by_clause]|[start_with_clause]|[group_by_clause])
{" " ([connect_by_clause]|[start_with_clause]|[group_by_clause])})
[order_siblings_by_clause]).
```

**[connect\_by\_clause](#connectbyclause)::=**

```ebnf
= CONNECT BY [NOCYCLE] condition.
```

**[start\_with\_clause](#startwithclause)::=**

```ebnf
= START WITH condition.
```

**[order\_siblings_by\_clause](#ordersiblingsbyclause)::=**

```ebnf
= ORDER SIBLINGS BY ((expr|position|c_alias) [ASC|DESC] [(NULLS FIRST)|(NULLS LAST)])
{"," ((expr|position|c_alias) [ASC|DESC] [(NULLS FIRST)|(NULLS LAST)])}.
```

**[group\_by\_clause](#groupbyclause)::=**

```ebnf
= (group_by_clause1|group_by_clause2) .
```

**[group\_by\_clause1](#groupbyclause)::=**

```ebnf
= GROUP BY ((expr){"," expr}|rollup_cube_clause|grouping_sets_clause) [HAVING condition].
```

**[rollup\_cube\_clause](#rollupcubeclause)::=**

```ebnf
= (ROLLUP|CUBE) "("(expr){"," expr}")".
```

**[grouping_sets_clause](#groupingsetsclause)::=**

```ebnf
= GROUPING SETS "("(expr){"," expr}")".
```

**[group\_by\_clause2](#groupbyclause)::=**

```ebnf
= [HAVING condition] [GROUP BY] ((expr){"," expr}|rollup_cube_clause|grouping_sets_clause).
```

**[set_oper](#setoper)::=**

```ebnf
= (UNION|INTERSECT|MINUS|EXCEPT) [ALL].
```

**[order\_by\_clause](#orderbyclause)::=**

```ebnf
= ORDER BY ((expr|position|c_alias) [ASC|DESC] [(NULLS FIRST)|(NULLS LAST)]) 
{"," ((expr|position|c_alias) [ASC|DESC] [(NULLS FIRST)|(NULLS LAST)])}.
```

**[row\_limiting\_clause](#rowlimitingclause)::=**

```ebnf
= [LIMIT expr [offset expr]].
```

**[offset\_fetch\_clause](#offsetfetchclause)::=**

```ebnf
= [OFFSET offset (ROW | ROWS)] [ FETCH [EXACT | APPROX | APPROXIMATE ](FIRST | NEXT) rowcount (ROW | ROWS) ONLY].
```

**[for\_update\_clause](#forupdateclause)::=**

```ebnf
= FOR UPDATE [OF [([schema "."] (table_name|view_name|synonym_name) ".")|t_alias] column_name]
[(NOWAIT)|(WAIT ntimes)|(SKIP LOCKED)].
```

<span id="subquery" name="subquery"></span>

### subquery

YashanDB的SELECT语句支持嵌套查询（对封装在`()`中的SELECT语句的结果进行SELECT查询）和合并查询（用集合合并多个SELECT语句的查询结果），不同方式的查询可交叉及多层级使用，例如：

```sql
SELECT * FROM (SELECT a,b FROM (SELECT cl_a a,cl_b b FROM table_a WHERE ...
               					UNION ALL
               					SELECT cl_c a,cl_d b FROM table_b WHERE ...
                               ) 
               WHERE ...
               )
WHERE ...
```

每一个完整的SELECT查询语句（包括其下级）均可称为一个子查询（subQuery）。 

对于子查询中的select_list列项：

- 若这些列项显式指定了别名（例如为列c1_a定义别名a），系统以此别名作为子查询返回结果集的列名，YashanDB对显式指定别名的长度限制为64字节。
- 若这些列项未显式指定别名，系统默认以该项列名作为子查询返回结果集的列名，且超过20字节截断，此时，如子查询select_list中存在名称前20字节相同的列，将导致返回结果集产生相同名称的列而触发系统报错。

示例

```sql
select * from (select
                 1111111111111111111111111111111111111111111111111111111111111111,
                 11111111111111111111111111111111111111111111111111111111111111112
               from dual);

[3:1]YAS-04301 ambiguous column
```

<span id="queryblock" name="queryblock"></span>

#### query\_block

含有单个SELECT的查询语句称为一个查询块（queryBlock）。 

<span id="withclause" name="withclause"></span>

##### with\_clause

该语句用于定义CTE（Common Table Expression），在某个子查询被多次使用时，可以直接使用CTE名称而不需要重复写该SELECT语句。

[行列转换](#pivotclause)所得的列不能作为递归CTE中递归部分的投影。

<span id="cteclause" name="cteclause"></span>

###### cte\_clause

CTE定义，包括：

*   cte\_name：名称，作为其他地方引用的标识。
*   column\_alias：定义CTE的列项，其中列字段应该来自于其后的子查询中定义的列项。
*   subquery：用于创建CTE的子查询，该子查询中不允许存在[pivot_clause](#pivotclause)。

示例

```sql
--WITH语句用于获取area表中'01'和'04'地区的代码和名称，在后面的查询中对branches表获取关联地区名称数据时，不需要多次写该语句，而只用和q_area做关联查询即可
WITH q_area(ano,aname) AS (SELECT area_no ano,area_name aname FROM area WHERE area_no IN ('01','04'))
SELECT bno,bname,aname FROM (
SELECT a.branch_no bno,a.branch_name bname,b.aname aname FROM branches a,q_area b WHERE a.area_no=b.ano AND a.area_no='01'
UNION
SELECT a.branch_no bno,a.branch_name bname,b.aname aname FROM branches a,q_area b WHERE a.area_no=b.ano AND a.area_no='04'
);
BNO   BNAME             ANAME       
----- ----------------- -------------
0101  Shanghai            EastChina          
0102  Nanjing            EastChina          
0103  Fuzhou            EastChina          
0104  Xiamen            EastChina          
0401  Beijing            NorthChina          
0402  Tianjin            NorthChina          
0403  Dalian            NorthChina          
0404  Shenyang            NorthChina

-- 在WITH语句中使用pivot进行行列转换
with CTE as (select * from employees pivot (count(employee_name) for sex in ('1',  '0'))) select * from cte;

BRANCH DEPARTMENT EMPLOYEE_NO   ENTRY_DATE                                                         '1'                   '0'
------ ---------- ------------- ------------------------------------------------ --------------------- ---------------------
0101   000        0101000001    2022-11-01                                                           1                     0
0101   000        0101000002    2020-02-05                                                           1                     0
0201   010        0201010011    2024-10-01                                                           0                     1
0201   008        0201008003    2023-08-28                                                           1                     0
0101   008        0201008004    2025-01-09                                                           1                     0
```

##### hint

该语句用于提出给定的方案到优化器（Optimizer），使其按照此方案生成语句的执行计划，详情请查阅[hint](../通用SQL语法/hint)。

##### distinct

该语句用于对查询结果进行过滤设置，对查询结果中重复的多条记录只返回一条记录。 

<span id="selectlist" name="selectlist"></span>

##### select\_list

该语句用于指定查询语句中要查询的列项，例如FROM后面某个表的某个列字段（Column）等。查询多项以`,`分隔。`*`表示查询FROM后面所有表|视图|AC|子查询等的所有列。 

AS alias用于定义别名，AS可省略。

<span id="exprclause" name="exprclause"></span>

###### expr\_clause

指定具体的列项，可以为：

* query\_block：将一个查询块语句的结果作为列项
* [expr](../通用SQL语法/expr)：表达式
*   CASE语句
*   udt_expr：对于Object UDT列，udt_expr格式为表别名.属性名；对于Varray UDT和Nested Table UDT列，udt_expr格式为表别名.*。详细使用方法和示例见[用户自定义类型](../数据类型/用户自定义类型)中描述。

示例（单机部署）

```sql
SELECT SYSDATE querying_date,
a.branch_name branch,
b.area_name area,
(SELECT DHQ FROM area where area_no='10') default_DHQ
FROM branches a, area b
WHERE a.area_no IN (SELECT area_no FROM area )
AND a.area_no=b.area_no;
QUERYING_DATE                    BRANCH          AREA                DEFAULT_DHQ      
-------------------------------- --------------- --------------------- ------
2022-01-10 10:24:35              Shanghai        EastChina                          
2022-01-10 10:24:35              Nanjing         EastChina                          
2022-01-10 10:24:35              Fuzhou          EastChina                          
2022-01-10 10:24:35              Xiamen          EastChina                          
2022-01-10 10:24:35              Beijing         NorthChina                          
2022-01-10 10:24:35              Tianjin         NorthChina                          
2022-01-10 10:24:35              Dalian          NorthChina                          
2022-01-10 10:24:35              Shenyang        NorthChina                          
2022-01-10 10:24:35              Chengdu         WestChina                          
2022-01-10 10:24:35              Changsha        CentralChina
```

示例（存算一体分布式集群部署）

```sql
SELECT SYSDATE querying_date,
a.branch_name branch,
b.area_name area,
'ShenZhen' default_DHQ
FROM branches a, area b
WHERE a.area_no IN ('01','02','03')
AND a.area_no=b.area_no;
QUERYING_DATE       BRANCH              AREA                   DEFAULT_DHQ         
------------------- ------------------- ---------------------- ----------
2022-01-23          Shanghai            EastChina              ShenZhen     
2022-01-23          Nanjing             EastChina              ShenZhen     
2022-01-23          Fuzhou              EastChina              ShenZhen     
2022-01-23          Xiamen              EastChina              ShenZhen     
2022-01-23          Chengdu             WestChina              ShenZhen
```

<span id="caseclause" name="caseclause"></span>

**case\_clause**

CASE表达式相当于编程语言中的IF ELSE，其写法有两种：Simple Case和Searched Case。

<span id="SimpleCase" name="SimpleCase"></span>

simple\_case\_expression = expr { WHEN comparison\_expr THEN return\_expr }.

searched\_case\_expression = { WHEN condition THEN return\_expr }.

else\_clause = ELSE else\_expr.

在Simple Case写法中，系统搜寻第一个与expr等值的comparision\_expr，并返回相对应的return\_expr。如果没有等值的comparision\_expr，并且存在ELSE子句，则返回else\_expr。否则返回NULL。

在Searched Case写法中，系统从左至右依次检查condition，并返回第一个相对应的return expr。如果所有condition皆为FALSE，并且存在ELSE子句，则返回else\_expr。否则返回NULL。

CASE语句中出现的所有表达式（else\_expr/return\_expr/expr/comparison\_expr，前缀名仅用于区分）均为通用表达式，其值在同一个语句中必须同属下列几种大类之一：

*   数值型，优先度从低到高的排序为：TINYINT、SMALLINT、INT、BIGINT、NUMBER、FLOAT、DOUBLE。
*   字符型，优先度从低到高的排序为：CHAR、VARCHAR。
*   日期时间型，优先度从低到高的排序为：DATE、TIMESTAMP。
*   其他数据类型归成一个大类。

在每个大类中，表达式的值将先统一为优先度高的数据类型，再进行匹配或返回。因此CASE表达式返回值的数据类型为所有表达式中优先度最高的类型。

示例

```sql
SELECT employee_name Name, CASE sex
WHEN '0' THEN 'Female'
WHEN '1' THEN 'Male'
END Sex
FROM employees;
NAME          SEX
------------- ------
Anna          Female
John          Male
Jack          Male
Jim           Male
 
--存在未列出的条件结果时，返回NULL                                                          
SELECT employee_name Name, CASE sex
WHEN '2' THEN 'Female'
WHEN '1' THEN 'Male'
END Sex
FROM employees;
NAME          SEX
------------- ---------
Anna
John          Male
Jack          Male
Jim           Male
 
--对所有不在列出条件的情况，给定默认值
SELECT employee_name Name, CASE sex
WHEN '2' THEN 'Female'
WHEN '1' THEN 'Male'
ELSE 'Unknown'
END Sex
FROM employees;
NAME          SEX
------------- ---------
Anna          Unknown
John          Male
Jack          Male
Jim           Male
```

<span id="tablereference" name="tablereference"></span>

##### table\_reference

该语句用于指定FROM语句中对查询对象的定义。

YashanDB支持为查询对象分别定义别名，查询对象可为如下类型：

*   [query\_name](#queryname)：表、视图、同义词、分区、子分区、AC等

*  [query_containers](#querycontainers)：containers()算子，可汇聚查询表、视图等，仅适用于容器数据库。在根容器上使用该算子可以汇聚所有PDB的同名查询对象相关数据，即执行全局查询。在PDB是使用该算子始终查询本地数据，效果等同于不加该算子。 

*   [subquery](#query_subquery)：一个子查询的结果

*   [dblink](../通用SQL语法/dblink/dblink语法说明.md)：远端表

查询对象不能同时存在行存表和列存表，否则返回错误。

<span id="queryname" name="queryname"></span>

###### query\_name

该语句用于指定表、视图、同义词、分区、子分区、AC等查询对象。

<span id="partitionextensionclause" name="partitionextensionclause"></span>

**partition\_extension\_clause**

对表分区和子分区的指定方式包括：

- 根据名称：直接获取分区或子分区对象。
- 根据提供的键值（Key Value）： 将键值与表分区界值比较，计算得到分区或子分区对象，键值可为界值范围内的任意值。键值的个数须与分区列个数对应，指定子分区时键值个数须与分区列和子分区列个数对应，多项用`,`分隔。

> **Note**: 
>
> 分布式样例表中未指定分区名，由数据库定义分区名及子分区名，可通过查看DBA_TAB_PARTITIONS/DBA_TAB_SUBPARTITIONS查看分区及子分区信息。

示例

```sql
-- 执行如下语句查询分区名称
SELECT partition_name
FROM DBA_TAB_PARTITIONS
WHERE table_name='SALES_INFO';
PARTITION_NAME
----------------------------------------------------------------
P_SALES_INFO_1
P_SALES_INFO_2
P_SALES_INFO_3

-- sales_info为一张分区表，可指定分区来查询其数据
SELECT year,month,branch,product,quantity,amount,salsperson FROM sales_info PARTITION (P_SALES_INFO_1);
YEAR  MONTH BRANCH PRODUCT      QUANTITY      AMOUNT SALSPERSON
----- ----- ------ --------- ----------- ----------- -------------
2021  10    0402   11001              20         300

SELECT year,month,branch,product,quantity,amount,salsperson FROM sales_info PARTITION FOR('0102');
YEAR  MONTH BRANCH PRODUCT      QUANTITY      AMOUNT SALSPERSON
----- ----- ------ --------- ----------- ----------- -------------
2001  01    0201   11001              30         500 0201010011
2000  12    0102   11001              20         300
2015  03    0102   11001              20         300

-- 获得sales_info表的子分区名称
SELECT partition_name,subpartition_name 
FROM DBA_TAB_SUBPARTITIONS
WHERE table_name='SALES_INFO';
-- 以下输出以单机为例
PARTITION_NAME               SUBPARTITION_NAME
---------------------------- ---------------------------------------
P_SALES_INFO_1               P_SALES_INFO_1_SP_SALES_INFO_1
P_SALES_INFO_1               P_SALES_INFO_1_SP_SALES_INFO_2
P_SALES_INFO_1               P_SALES_INFO_1_SP_SALES_INFO_3
P_SALES_INFO_2               P_SALES_INFO_2_SP_SALES_INFO_1
P_SALES_INFO_2               P_SALES_INFO_2_SP_SALES_INFO_2
P_SALES_INFO_2               P_SALES_INFO_2_SP_SALES_INFO_3
P_SALES_INFO_3               P_SALES_INFO_3_SP_SALES_INFO_1
P_SALES_INFO_3               P_SALES_INFO_3_SP_SALES_INFO_2
P_SALES_INFO_3               P_SALES_INFO_3_SP_SALES_INFO_3

-- 选择其中一个子分区指定查询数据
SELECT year,month,branch,product,quantity,amount,salsperson FROM sales_info SUBPARTITION (P_SALES_INFO_1_SP_SALES_INFO_2);
YEAR  MONTH BRANCH PRODUCT      QUANTITY      AMOUNT SALSPERSON
----- ----- ------ --------- ----------- ----------- -------------
2021  10    0402   11001              20         300
```

<span id="queryslice" name="queryslice"></span>

**指定切片查询**

对于LSC表，支持通过指定Slice ID查询某个切片的数据，具体规则为：

- 只能对LSC表指定切片查询，否则报错。
- 对于分区表，指定切片的同时，必须指定分区，否则报错。
- Slice ID为0表示查询可变数据区（MCOL）的数据。
- 通过查询V$LSC_SLICE_STAT视图可获得某张LSC表（分区）包含的所有Slice ID。

示例（LSC表）

```sql
-- orders_info表的分区中存在如下稳态数据
SELECT order_no,product_no,area,branch,order_date,salesperson,id FROM orders_info PARTITION(p_orders_info_1);
ORDER_NO          PRODUCT_NO AREA  BRANCH ORDER_DATE            SALESPERSON            ID
----------------- ---------- ----- ------ --------------------- ------------- -----------
20010102020001    11001      01    0101   2023-04-05            0201010011            300
20210102020002    11001      04    0401   2023-04-05            0201008003            400
20210102020002    11002      05    0501   2023-04-05            0201010011            200
20210102020002    10001      01    0102   2023-04-05            0201008003            100

-- 新插入orders_info表一条数据，将存储在可变数据区
INSERT INTO orders_info VALUES ('20210102020003','10002','03','0701',SYSDATE-400,'0201008003',900);

-- 查询V$LSC_SLICE_STAT视图获得Slice ID
SELECT utp.table_name, utp.partition_name, lss.slice_id
FROM user_tab_partitions utp, v$lsc_slice_stat lss, user_objects uo
WHERE utp.table_name = 'ORDERS_INFO'
AND uo.subobject_name = utp.partition_name
AND uo.object_id = lss.obj;
TABLE_NAME        PARTITION_NAME        SLICE_ID 
----------------- --------------------  ---------- 
ORDERS_INFO       P_ORDERS_INFO_1       0
ORDERS_INFO       SYS_P12               0
ORDERS_INFO       SYS_P13               0

-- 查询MCOL数据
SELECT order_no,product_no,area,branch,order_date,salesperson,id FROM orders_info PARTITION(p_orders_info_1) SLICE (0);
ORDER_NO          PRODUCT_NO AREA  BRANCH ORDER_DATE            SALESPERSON            ID
----------------- ---------- ----- ------ --------------------- ------------- -----------
20010102020001    11001      01    0101   2023-04-05            0201010011            300
20210102020002    11001      04    0401   2023-04-05            0201008003            400
20210102020002    11002      05    0501   2023-04-05            0201010011            200
20210102020002    10001      01    0102   2023-04-05            0201008003            100
20210102020003    10002      03    0701   2023-04-05            0201008003            500
```

<span id="tablecollectionexpression" name="tablecollectionexpression"></span>

**table_collection_expression**

该语句可用于执行如下两种情况：

- UDT
- 表函数table()

**UDT**

可用于查询Varray UDT或Nested Table UDT数据，UDT数据类型的详细描述请参考[用户自定义类型](../数据类型/用户自定义类型)。

执行此类查询时，collection_expression为一个Varray对象或Nested Table对象，语法格式为表别名.UDT列名。

Varray对象依赖于所在表，因此应保证执行计划最先访问主表。

示例（HEAP表）

```sql
DROP TABLE IF EXISTS city_intro;
CREATE OR REPLACE TYPE brc_array AS VARRAY(20) OF CHAR(15);
/
CREATE OR REPLACE TYPE city_table AS TABLE OF CHAR(10);
/

--创建包含Varray UDT和Nested Table UDT列的city_intro表
CREATE TABLE city_intro (id INT, branches brc_array, citys city_table)
NESTED TABLE citys STORE AS nt_citys;

--插入数据
INSERT INTO city_intro 
VALUES (1,
        brc_array('branch01','branch02','branch03'),
        city_table('shenzhen','guangzhou','dongguan'));
                  
--查询数据
SELECT /*+ leading(c) */ c.id, cbranch.*, ccity.*
FROM city_intro c,
TABLE(c.branches) cbranch,
TABLE(c.citys) ccity;
          ID COLUMN_VALUE      COLUMN_VALUE  
------------ ----------------- ------------- 
           1 branch01          shenzhen     
           1 branch02          shenzhen     
           1 branch03          shenzhen     
           1 branch01          guangzhou    
           1 branch02          guangzhou    
           1 branch03          guangzhou    
           1 branch01          dongguan     
           1 branch02          dongguan     
           1 branch03          dongguan  
```

**表函数table()**

可使用表函数table()进行查询，表函数的具体描述请查阅[内置表函数](../内置函数/00内置函数.md#tableFunc)。

执行此类查询时，collection_expression为所需表函数。

<span id="query_subquery" name="query_subquery"></span>

###### subquery

该语句用于指定一个子查询结果。

示例（单机/共享集群/分布式集群部署）

```sql
-- 为sales_info表创建一个同义词，也可指定分区来查询其数据
CREATE SYNONYM sy_sales_info FOR sales_info;
SELECT year,month,branch,product,quantity,amount,salsperson FROM sy_sales_info PARTITION(p_sales_info_1);
YEAR  MONTH BRANCH PRODUCT      QUANTITY      AMOUNT SALSPERSON
----- ----- ------ --------- ----------- ----------- -------------
2021  10    0402   11001              20         300
 
-- 用于子查询中
SELECT b.branch_name branch,
sum(s.amount) salesamount
FROM branches b,
(SELECT * FROM sy_sales_info PARTITION(p_sales_info_2)) s
WHERE b.branch_no=s.branch
GROUP BY b.branch_name;
BRANCH                                                           SALESAMOUNT
---------------------------------------------------------------- ----------- 
Chengdu                                                                   500
Nanjing                                                                   600
```

<span id="sampleclause" name="sampleclause"></span>

###### sample_clause

SAMPLE用于从表中按比例抽取一个随机样本，后续WHERE条件将基于这个样本数据而不是整张表数据。

本语句可作用于单个物理表，或者基于单个物理表的视图，对于多表连接的视图执行SAMPLE将会报错。

存算一体分布式集群部署中不可使用本语句。

- **sample_percent**

  指定样本所占的百分比，其值必须为一个[0.000001, 100]区间的常量数值（支持可以隐式转换为数值的其他类型）。此百分比代表采样时每个数据行被选为样本的概率，该概率为统计学上的概念，这意味着系统并不会精确返回基于sample\_percent计算出的行数的记录。

- **SEED seed_value**
  
  本语句定义一个种子值，系统将尝试对相同的种子值返回相同的样本，未指定本语句时系统每次返回的样本是随机的。seed\_value的值必须为一个[0, 4294967295]区间的常量整数（支持可以隐式转换为数值的其他类型，支持将小数截断为整数），其中大于4294967295的数值按4294967295处理。

示例（HEAP表）

```sql
-- 随机返回样本
SELECT area_no,area_name,DHQ FROM area SAMPLE(60);
AREA_NO AREA_NAME                 DHQ                   
------- ------------------------- --------------------- 
03      SouthChina                    Guangzhou            
04      NorthChina                    Beijing              
05      CentralChina                    Wuhan  

-- 指定相同seed返回相同样本
SELECT area_no,area_name,DHQ FROM area SAMPLE(60) SEED(7);
AREA_NO AREA_NAME               DHQ                   
------- ----------------------- --------------------- 
01      EastChina                  Shanghai             
02      WestChina                  Chengdu              
03      SouthChina                  Guangzhou            
04      NorthChina                  Beijing              
05      CentralChina                  Wuhan

SELECT area_no,area_name,DHQ FROM area SAMPLE(60) SEED(10);
AREA_NO AREA_NAME              DHQ                   
------- ---------------------- --------------------- 
01      EastChina                 Shanghai             
03      SouthChina                 Guangzhou            
04      NorthChina                 Beijing   

SELECT area_no,area_name,DHQ FROM area SAMPLE(60) SEED(7);
AREA_NO AREA_NAME               DHQ                   
------- ----------------------- --------------------- 
01      EastChina                  Shanghai             
02      WestChina                  Chengdu              
03      SouthChina                  Guangzhou            
04      NorthChina                  Beijing              
05      CentralChina                  Wuhan
```

<span id="querycontainers" name="querycontainers"></span>

###### query_containers

该语句用于在容器数据库中汇聚查询PDB中的表、视图等查询对象。  

在根容器上使用该算子可汇聚所有容器（根容器和所有PDB）中同名查询对象的相关数据，实现全局查询功能，查询结果中将生成伪列CON_ID以区分数据来源。在PDB中使用该算子时，则始终查询本地数据，其效果等同于不使用该算子。

 

如需查询全局的汇聚数据，需满足以下要求：

- 具备所有查询对象的相应权限。

- PDB中的查询对象应与根容器中的相应对象具有相同的表结构定义。

- 若汇聚查询的列项（select_list）中存在LOB类型列，整个查询结果集将为NULL。



示例（单机/共享集群/分布式集群部署）

```sql
-- 获取PDB信息
show pdbs

               CON_ID CON_NAME                                                         STATUS
--------------------- ---------------------------------------------------------------- -----------------
                    1 PDB$SEED                                                         CLOSED
                    2 PDB1                                                             OPEN

-- 在根容器上全局查询v$parameter视图，获取所有PDB的某个参数配置
select con_id,name,value from containers(v$parameter) where name ='DB_BLOCK_SIZE';

      CON_ID NAME                                                             VALUE
------------ ---------------------------------------------------------------- ----------------------------------------------------------------
           0 DB_BLOCK_SIZE                                                    8192
           2 DB_BLOCK_SIZE                                                    8192
```

<span id="flashbackqueryclause" name="flashbackqueryclause"></span>

###### flashback\_query\_clause

YashanDB支持对近期历史数据的追溯查询，例如某条记录在某个时间点已被UPDATE或DELETE更改，但通过flashback\_query\_clause可以获得该时间点之前一段时间范围内（这取决于undo空间的清理规则），该条记录被更改之前的数据值。

可以使用SCN号或TIMESTAMP标签来定位到想要追溯查询的行记录。

系统在记录TIMESTAMP时使用的是北京时间。

存算一体分布式集群部署中不可使用本语句。

示例（单机/共享集群/分布式集群部署）

```sql
-- area表中存在的一条记录
SELECT area_no,area_name,DHQ FROM area WHERE area_no='03';
AREA_NO AREA_NAME          DHQ         
------- ------------------ ------------
03      SouthChina             Guangzhou       
 
-- 获取当前时间
SELECT SYSTIMESTAMP res FROM dual;
RES                                            
----------------------------------------------------------------
2023-12-17 14:14:08.498126 +08:00    
 
-- 删除此条记录并提交
DELETE FROM area WHERE area_no='03';
COMMIT;
SELECT area_no,area_name,DHQ FROM area WHERE area_no='03';
AREA_NO AREA_NAME          DHQ         
------- ------------------ ------------
 
0 rows fetched.
 
-- 利用flashback可查询到快照历史数据
SELECT area_no,area_name,DHQ FROM area AS OF TIMESTAMP TIMESTAMP('2023-12-17 14:14:08.498126')
WHERE area_no='03';
AREA_NO AREA_NAME          DHQ         
------- ------------------ ------------
03      SouthChina             Guangzhou     
```

<span id="pivotclause" name="pivotclause"></span>

###### pivot\_clause

PIVOT用于行列转换，通过从结果集中抽出FOR后的列，根据IN里的常量表达式列表构成新的投影列，并结合聚合函数输出对应聚合列的结果。

PIVOT可以对任意结果集（即单表查询或多表连接的结果）进行操作。

pivot_clause前查询的结果集若为多表，只允许使用JOIN ON进行显式join，不允许使用逗号进行隐式join。

IN后的列名的长度大于64字符时，输出的列名会自动截取长度64后的字符。

- **aggregate_expression**

  aggregate_expression的聚合列与FOR后的列必须出现在查询的投影列中。

  aggregate_expression中使用的聚合函数不允许出现GROUPING_ID、GROUP_ID、GROUPING函数、PERCENTILE_CONT函数、MEDIAN函数以及GROUP_CONCAT的相关函数（GROUP_CONCAT、WM_CONCAT、LISTAGG、STRING_AGG），非聚合函数或无聚合函数时报错。

  aggregate_expression的聚合列与IN后的列的别名长度上限为64字符，超过64字符时报错。

- **column_expression**

  必须为简单列名，即不允许使用table.column的形式。

- **const_expression**

  必须为常量表达式。

示例

```SQL
-- 抽取区域编号列，并统计对应区域的数量
SELECT * FROM (SELECT branch_name, area_no FROM branches) 
PIVOT(
  COUNT(branch_name)
  FOR area_no 
  IN ('01' as EastChina, '02' as WestChina, '03' as SouthChina, '04' as NorthChina, '05' as CentralChina)
);
EastChina                WestChina                SouthChina                NorthChina                CentralChina
--------------------- --------------------- --------------------- --------------------- ---------------------
                    4                     1                     0                     4                     1

1 row fetched.

-- 显示华东和华西地区最大编号与城市数量
SELECT * FROM (SELECT branch_no, branch_name, area_no FROM branches) 
PIVOT(
  COUNT(branch_name) Citys,
  MAX(branch_no) MaxNO 
  FOR area_no 
  IN ('01' as EastChina, '02' as WestChina)
);
      EASTCHINA_CITYS EASTCHINA_MAXNO       WESTCHINA_CITYS WESTCHINA_MAXNO
--------------------- --------------- --------------------- ---------------
                    4 0104                                1 0201

1 row fetched.
```

<span id="unpivotclause" name="unpivotclause"></span>

###### unpivot\_clause

UNPIVOT用于行列转换，将列转化为行。该语句仅适用于HEAP表。

UNPIVOT可以对任意结果集（即单表查询或多表连接的结果）进行操作。

- **INCLUDE | EXCLUDE NULLS**

  该字段表示是否排除转换列有NULL值的行，默认为EXCLUDE NULLS。

- **value_column**

  定义列名，用于存储转换列的列值，必须为简单列名，即不允许使用table.column的形式。

- **pivot_column**

  定义列名，用于存储转换列的列名，必须为简单列名。

- **unpivot_in_clause**

  in子句中，为转换列的列名及别名，将作为pivot_column列的值。

示例（HEAP表）

```SQL
-- 创建pivot_table表，该表统计每种产品的总销售价格。
CREATE TABLE pivot_table AS
SELECT *
FROM (SELECT product_no, product_name, price FROM product) PIVOT
       (SUM(price) FOR product_name IN ('product001' AS product001, 'product002' AS product002));
select * from pivot_table ORDER BY product_no;
PRODUCT_NO  PRODUCT001  PRODUCT002
---------- ----------- -----------
11001               10
11002                           16
10001              100
10002                          200

4 rows fetched.

-- unpivot将product001，product002列旋转成price和product_name列，price列存储旋转列的值，product_name存储旋转列的别名。默认排除NULL值。
SELECT *
FROM pivot_table UNPIVOT (price FOR product_name IN (product001 AS 'product001',
           product002 AS 'product002'))
ORDER BY product_no;
PRODUCT_NO PRODUCT_NAME        PRICE
---------- ------------- -----------
10001      product001            100
10002      product002            200
11001      product001             10
11002      product002             16

4 rows fetched.

-- INCLUDE NULLS包含NULL值。
SELECT *
FROM pivot_table UNPIVOT INCLUDE NULLS (price FOR product_name IN (product001 AS 'product001',
           product002 AS 'product002'))
ORDER BY product_no;
PRODUCT_NO PRODUCT_NAME        PRICE
---------- ------------- -----------
10001      product001            100
10001      product002
10002      product001
10002      product002            200
11001      product001             10
11001      product002
11002      product001
11002      product002             16

8 rows fetched.
```

<span id="joinclause" name="joinclause"></span>

##### join\_clause

该语句用于将多个表、视图、AC和子查询进行连接（Join）查询，有如下几种Join方式：

*   显式指定JOIN关键字进行连接查询，并通过指定INNER|OUTER关键字分为内连接（Inner Join）和外连接（Outer Join）两种方式，未指定时缺省为内连接。
*   在FROM后面将各表、视图、子查询以`,`分隔，而在WHERE后指定条件以获取交叉连接后的查询结果，此种方式等同于内连接。
*   在WHERE后条件子句中指定(+)操作符，或者在JOIN ON后子句中指定(+)操作符，表示外连接。

存算一体分布式集群部署中遵循如下规则：

- 多表连接查询分布式系统视图和DBA视图、系统表时，使用的是各节点本地数据。
- 分布式视图仅允许和分布式视图、DBA视图、USER视图、ALL视图、系统表和单机视图进行多表查询。
- 多表查询分布式系统视图时，如使用order by、group by、窗口函数、聚集函数和limit，都仅在各节点内生效，最终结果可能不满足有序等约束。

<span id="innercrossjoinclause" name="innercrossjoinclause"></span>

###### inner\_cross\_join\_clause

内连接查询是一种交叉连接查询方式，对A和B进行内连接查询时，将A的每一行与B的每一行按照连接条件进行比较，返回的是A、B均满足条件的结果。其中：

*   INNER：产生的结果是AB的交集。此时必须在后面指定ON条件。
*   CROSS：产生的结果是AB的笛卡尔积。此时不可以在后面指定ON条件。

示例（存算一体分布式集群部署）

```sql
-- INNER JOIN、INNER关键字可省略
SELECT b.branch_name, a.area_name
FROM branches b
INNER JOIN area a
ON a.area_no = b.area_no
WHERE b.branch_no like '01%';
BRANCH_NAME       AREA_NAME                                                     
----------------- --------------------
Shanghai               EastChina                                                       
Nanjing               EastChina                                                       
Fuzhou               EastChina                                                       
Xiamen               EastChina                                                                  
 
-- 等同于上面的内连接
SELECT b.branch_name, a.area_name
FROM branches b, area a
WHERE a.area_no=b.area_no AND b.branch_no like '01%';
BRANCH_NAME       AREA_NAME                                                     
----------------- --------------------
Shanghai               EastChina                                                       
Nanjing               EastChina                                                       
Fuzhou               EastChina                                                       
Xiamen               EastChina                                                       
 
-- CROSS JOIN
SELECT b.branch_name, a.area_name
FROM branches b
CROSS JOIN area a
WHERE b.branch_no like '01%';
BRANCH_NAME       AREA_NAME                                                     
----------------- --------------------
Shanghai               EastChina                                                       
Shanghai               WestChina                                                       
Shanghai               SouthChina                                                       
Shanghai               NorthChina                                                       
Shanghai               CentralChina                                                       
Nanjing               EastChina                                                       
Nanjing               WestChina                                                       
Nanjing               SouthChina                                                       
Nanjing               NorthChina                                                       
Nanjing               CentralChina                                                       
Fuzhou               EastChina                                                       
Fuzhou               WestChina                                                       
Fuzhou               SouthChina                                                       
Fuzhou               NorthChina                                                       
Fuzhou               CentralChina                                                       
Xiamen               EastChina                                                       
Xiamen               WestChina                                                       
Xiamen               SouthChina                                                       
Xiamen               NorthChina                                                       
Xiamen               CentralChina           

-- 分布式视图查询                                                
select * from (select GROUP_ID,GROUP_NODE_ID,NAME,VALUE,DEFAULT_VALUE,IS_DEPRECATED from GV$SYSTEM_PARAMETER where GROUP_ID=0);
GROUP_ID GROUP_NODE_ID NAME          VALUE         DEFAULT_VALUE           IS_DEPRECATED
-------- ------------- ------------- ------------- ----------------------- -------------
```

<span id="outerjoinclause" name="outerjoinclause"></span>

###### outer\_join\_clause

外连接查询指定了一张主表，对主表中的记录不要求在对方表里能找到匹配记录，而是全部做为有效结果返回，在返回结果集中对于对方未找到匹配值的列项设为NULL值。外连接需要显式地指定左连接或右连接。

<span id="outerjointype" name="outerjointype"></span>

**outer\_join\_type**

外连接类型包括：

- **LEFT \[OUTER\] JOIN**

  左连接查询，当A LEFT OUTER JOIN B时，以A为主表，A的每一行记录均会出现在查询结果集中，对于在B中匹配不到的值设为NULL。

- **RIGHT \[OUTER\] JOIN**

  右连接查询，当A RIGHT OUTER JOIN B时，以B为主表，B的每一行记录均会出现在查询结果集中，对于在A中匹配不到的值设为NULL。

- **FULL \[OUTER\] JOIN**

  全连接查询，当A FULL OUTER JOIN B时，A和B的每一行记录均会出现在查询结果集中，对于在A中和B中匹配不到的值设为NULL。

示例

```sql
--左连接，对于branches表中的Wuhan，未在area表中匹配到对应的地区记录，则在结果集中将地区设为NULL
SELECT b.branch_name, a.area_name
FROM branches b
LEFT OUTER JOIN area a
ON a.area_no = b.area_no
WHERE b.branch_no LIKE '01%' OR b.branch_no LIKE '05%';
BRANCH_NAME        AREA_NAME     
------------------ ---------------
Shanghai             EastChina            
Nanjing             EastChina            
Fuzhou             EastChina            
Xiamen             EastChina            
Wuhan                             
Changsha             CentralChina            
                                   
--右连接，对于area表中的SouthChina地区，未在branches表中匹配到下属的城市记录，则在结果集中将城市设为NULL
SELECT b.branch_name, a.area_name
FROM branches b
RIGHT OUTER JOIN area a
ON a.area_no = b.area_no
WHERE a.area_no IN ('01','03','05');
BRANCH_NAME         AREA_NAME    
------------------- -------------
Xiamen              EastChina           
Fuzhou              EastChina           
Nanjing              EastChina           
Shanghai              EastChina           
                    SouthChina         
Changsha              CentralChina
                                   
--全连接，对于area表中的SouthChina地区，未在branches表中匹配到下属的城市记录，则在结果集中将城市设为NULL
SELECT b.branch_name, a.area_name
FROM branches b
FULL OUTER JOIN area a
ON a.area_no = b.area_no
WHERE a.area_no IN ('01','03','05');
BRANCH_NAME         AREA_NAME    
------------------- -------------
Xiamen              EastChina           
Fuzhou              EastChina           
Nanjing              EastChina           
Shanghai              EastChina
                    SouthChina    
Changsha              CentralChina            
```

**(+) operator**

YashanDB支持通过(+)操作符指定外连接。

(+)可以使用在where\_clause和join\_clause的condition子句的比较条件中。

在比较条件中，(+)作用于某一个列字段上，表示如果比较条件无法满足，则对该列对应的表进行补空，与外连接同义，即比较条件对端所涉及的表（可能为一个或多个）对该表进行外连接。

(+)操作符的使用限制如下：

- (+)操作符只能作用于列字段上，不可以作用于表达式。
- 在where\_clause条件中使用(+)操作符时，不能与ANSI标准的Join语法一起使用。 
- 在join\_clause条件中使用(+)操作符时，连接的两个表必须为普通表，不能为多表Join之后的中间结果。 
- 不允许使用(+)操作符对两个表互相外连接。
- 某个条件中存在(+)操作符时，该条件的同级条件之间只允许使用AND关键词连接。

示例

```sql
--(+)作用于area表的area_no上，意为branches表对area表进行左外连接，结果集对area表中没有匹配上的数据进行补空。
--对于branches表中的Wuhan，未在area表中匹配到对应的地区记录，则在结果集中将地区设为NULL
SELECT a.area_name,b.branch_name 
FROM area a, branches b
WHERE b.area_no = a.area_no(+)
AND (b.branch_no LIKE '01%' OR b.branch_no LIKE '05%');
AREA_NAME        BRANCH_NAME  
---------------- -------------
EastChina           Shanghai           
EastChina           Nanjing           
EastChina           Fuzhou           
EastChina           Xiamen           
                 Wuhan         
CentralChina           Changsha    

SELECT a.area_name, b.branch_name
FROM branches b
JOIN area a
ON b.area_no = a.area_no(+)
WHERE b.branch_no LIKE '01%' OR b.branch_no LIKE '05%';
AREA_NAME        BRANCH_NAME  
---------------- -------------
EastChina           Shanghai           
EastChina           Nanjing           
EastChina           Fuzhou           
EastChina           Xiamen           
                 Wuhan         
CentralChina           Changsha    
```

<span id="whereclause" name="whereclause"></span>

##### where\_clause

该语句用于指定查询的[condition](../通用SQL语法/condition)。

<span id="hierarchicalqueryclause" name="hierarchicalqueryclause"></span>

##### hierarchical\_query\_clause

该语句用于指定层次（父子）关系的条件查询。通过START WITH获取根数据，通过CONNECT BY指定父子关系，同时还可以结合[group_by_clause](#groupbyclause)子句进行分类，将所有满足层次关系的数据全部查找出来。

该语句仅适用于HEAP表。

YashanDB提供如下虚拟列（非表结构中定义的列，但可以被当作列字段进行查询）供层次关系查询中使用：

- **LEVEL**

  LEVEL表示层次查询形成的树结构中的当前层数。该列值一直从1开始，即START WITH对应的数据的层数一直为1，之后子孙节点的LEVEL值依次递增。

- **CONNECT_BY_ISLEAF**

  CONNECT_BY_ISLEAF表示当前数据是否是层次查询形成的树结构中的叶子节点。若是叶子节点值为1，否则为0。

- **CONNECT_BY_ISCYCLE**

  CONNECT_BY_ISCYCLE表示在层次查询中当前数据是否会导致形成环，即根据层次关系，当前层数据是否存在其叶子节点也是其父节点。该列只有在同时指定NOCYCLE关键字时才有意义，当前数据会导致形成环则结果为1，否则为0。

YashanDB提供如下标识符用来指定层次关系中的某个节点属性：

- **PRIOR**

  PRIOR操作符之后的参数将作为层次查询中的父节点。参数不能为虚拟列、层次查询函数、操作符、伪列及子查询。PRIOR通常应用于connect_by_clause中，且不能在start_with_clause中使用。

- **CONNECT_BY_ROOT**

  CONNECT_BY_ROOT操作符之后的参数将作为在层次查询中的根节点。参数不能为虚拟列、层次查询函数、操作符、伪列及子查询。CONNECT_BY_ROOT不能在connect_by_clause和start_with_clause中使用。

YashanDB提供如下层次查询中的专用函数：

- **SYS_CONNECT_BY_PATH**

  `SYS_CONNECT_BY_PATH(col_name, delimiter)`，其中delimiter 表示分隔符，只能使用字面量。该函数将获取从根节点到当前节点的路径上所有节点名为col_name的值，中间用delimiter进行分隔开。SYS_CONNECT_BY_PATH不能在connect_by_clause、start_with_clause和group_by_clause中使用。

<span id="connectbyclause" name="connectbyclause"></span>

###### connect_by_clause

通过CONNECT BY后跟的[condition](../通用SQL语法/condition)，指定层次关系查询条件，其中必须至少有一个条件用于指定父子关系，该条件通过PRIOR标识符识别。 

**NOCYCLE**

当根据指定的条件关系获得的查询结果存在环时，如不指定NOCYCLE，系统将返回错误，指定时系统将忽略环的问题，仍返回所有数据。

<span id="startwithclause" name="startwithclause"></span>

###### start_with_clause

通过START WITH后跟的[condition](../通用SQL语法/condition)，指定以满足该条件的数据作为层次关系中根节点，其LEVEL为1。condition中不允许使用rownum。

本语句可省略，则表示将所有数据均作为根节点进行层次关系查询。

使用本语句时，其前面或后面必须存在connect_by_clause，不能独立使用。

<span id="ordersiblingsbyclause" name="ordersiblingsbyclause"></span>

###### order_siblings_by_clause

对同一父节点下的同一层次数据，通过ORDER SIBLINGS BY指定排序列和排序规则进行排序，本语句功能同order_by_clause。

使用本语句时，其前面必须存在connect_by_clause，不能独立使用。

示例（HEAP表）

```sql
--建立包含层次关系的地区表area_info
CREATE TABLE area_info (id INT, father_id INT, area_name VARCHAR(20));
INSERT INTO area_info values(1,   0, 'Guangdong');
INSERT INTO area_info values(755, 1, 'Shenzhen');
INSERT INTO area_info values(756, 755, 'Longhua');
INSERT INTO area_info values(757, 755, 'Futian');
INSERT INTO area_info values(2,   0, 'Zhejiang');
INSERT INTO area_info values(571, 2, 'Hangzhou');
COMMIT;

--显示地区层次关系（指定根节点）   
SELECT id, father_id, LEVEL,
CONNECT_BY_ROOT area_name as name, 
SYS_CONNECT_BY_PATH(area_name, '/') path  
FROM area_info  
CONNECT BY id<>757 
AND PRIOR id = father_id 
START WITH father_id = 0;  
    ID    FATHER_ID      LEVEL NAME                  PATH
------ ------------ ---------- --------------------- -----------------------------------
     1            0          1 Guangdong             /Guangdong
   755            1          2 Guangdong             /Guangdong/Shenzhen
   756          755          3 Guangdong             /Guangdong/Shenzhen/Longhua
     2            0          1 Zhejiang              /Zhejiang
   571            2          2 Zhejiang              /Zhejiang/Hangzhou   
  
--显示地区层次关系（不指定根节点，则所有数据都将作为根节点）   
SELECT id, father_id, LEVEL,
CONNECT_BY_ROOT area_name as name, 
SYS_CONNECT_BY_PATH(area_name, '/') path  
FROM area_info  
CONNECT BY id<>757 
AND PRIOR id = father_id;  
    ID    FATHER_ID      LEVEL NAME          PATH             
------ ------------ ---------- ------------- ---------------- 
     1            0          1 Guangdong     /Guangdong              
   755            1          2 Guangdong     /Guangdong/Shenzhen        
   756          755          3 Guangdong     /Guangdong/Shenzhen/Longhua   
   755            1          1 Shenzhen      /Shenzhen              
   756          755          2 Shenzhen      /Shenzhen/Longhua        
     2            0          1 Zhejiang      /Zhejiang              
   571            2          2 Zhejiang      /Zhejiang/Hangzhou        
   571            2          1 Hangzhou      /Hangzhou              
   756          755          1 Longhua       /Longhua              
   757          755          1 Futian        /Futian       

--显示节点是否为叶子节点及是否出现循环  
SELECT id, father_id, level, 
CONNECT_BY_ISLEAF as leaf, CONNECT_BY_ISCYCLE as iscycle  
FROM area_info  
CONNECT BY NOCYCLE id<>757 
AND PRIOR id = father_id 
START WITH father_id = 0;  
    ID    FATHER_ID     LEVEL LEAF      ISCYCLE     
------ ------------ --------- --------- ----------- 
     1            0         1 false     false      
   755            1         2 false     false      
   756          755         3 true      false      
     2            0         1 false     false      
   571            2         2 true      false      

--按指定列对同层次数据排序  
SELECT  id, father_id, LEVEL,
CONNECT_BY_ROOT area_name as name, 
SYS_CONNECT_BY_PATH(area_name, '/') path    
FROM area_info  
CONNECT BY PRIOR id = father_id START WITH father_id = 0  
ORDER SIBLINGS BY id DESC;  
    ID    FATHER_ID    LEVEL NAME         PATH             
------ ------------ -------- ------------ --------------- 
     2            0        1 Zhejiang       /Zhejiang            
   571            2        2 Zhejiang       /Zhejiang/Hangzhou       
     1            0        1 Guangdong      /Guangdong            
   755            1        2 Guangdong      /Guangdong/Shenzhen       
   757          755        3 Guangdong      /Guangdong/Shenzhen/Futian  
   756          755        3 Guangdong      /Guangdong/Shenzhen/Longhua  
```

<span id="groupbyclause" name="groupbyclause"></span>

##### group\_by\_clause

该语句用于对查询结果集进行按条件的分类聚集（Aggregation）。

###### GROUP BY

在GROUP BY后定义分组列，多个列用`,`分隔，同时需满足如下规则：

- UDT、内置的UDT均不能作为分组列。

*   在select\_list中出现的查询列或列数据，必须为分组列或列数据的子集。

    列是子集："SELECT col ,COUNT(\*) FROM table GROUP BY col, col2；"

    列数据是子集："SELECT LPAD(col), COUNT(\*) FROM table GROUP BY col；"

*   查询列与分组列里出现函数时，函数的参数必须一致。

    此语句错误："SELECT SUBSTR(col, 1,1), COUNT(\*) FROM table GROUP BY SUBSTR(col, 1,2);"

*   如果同时出现了DISTINCT或ORDER BY子语句，则在它们中出现的列遵循上两条规则。
*   分组列不能包含或嵌套\*（星号），SEQUENCE，子查询及聚集函数等表达式。
*   当分组列为数字时，与ORDER BY不同的是，本语句不会将数字释义成列的位置，而是作为字面量处理。

###### HAVING

HAVING子句约束SELECT查询语句中GROUP BY的结果，该约束应用于查询结果中的每个分组，类似WHERE条件应用于select\_list。使用规则如下：

* HAVING子句可以放在GROUP BY子句的前面或后面。
* HAVING后的condition是一个布尔表达式，语法同WHERE子句中的filter\_clause，但是它只能包含分组列、聚集函数（可以与select\_list中的聚集函数不一致）、字面量和子查询（子查询中的列不需要为分组列）。其中存算一体分布式集群部署中不可以使用子查询。
*   如果没有GROUP BY，直接使用HAVING子句，表示该约束作用于整个查询结果，此时select\_list和condition中不能出现分组列。

示例（单机部署）

```sql
-- 按年份对销售额进行汇总，HAVING后可以指定其他聚集函数，且可以指定对其他表的子查询
SELECT 1,year,month,CONCAT(year,month),SUM(amount) FROM sales_info HAVING MAX(amount)>100 GROUP BY year,month;
           1 YEAR  MONTH CONCAT(YEAR,MONTH)           SUM(AMOUNT)
------------ ----- ----- ------------------ ---------------------
           1 2000  12    200012                               300
           1 2001  01    200101                               500
           1 2015  03    201503                               300
           1 2015  11    201511                               300
           1 2021  05    202105                               600
           1 2021  10    202110                               300
 
SELECT 1,year,month,CONCAT(year,month),SUM(amount)
FROM sales_info
GROUP BY year,month
HAVING SUM(amount)>(SELECT MIN(price*50) FROM product);
           1 YEAR  MONTH CONCAT(YEAR,MONTH)           SUM(AMOUNT)
------------ ----- ----- ------------------ ---------------------
           1 2021  05    202105                               600
 
-- HAVING定义分组列的条件
SELECT 1,year,month,CONCAT(year,month),SUM(amount)
FROM sales_info
GROUP BY year,month
HAVING year>'2015';
           1 YEAR  MONTH CONCAT(YEAR,MONTH)           SUM(AMOUNT)
------------ ----- ----- ------------------ ---------------------
           1 2021  05    202105                               600
           1 2021  10    202110                               300
 
-- 单独使用HAVING语句，定义对整个查询结果的聚集约束条件,此时不能出现分组列
SELECT 1,SUM(amount)
FROM sales_info
HAVING MAX(amount)>100;
           1           SUM(AMOUNT)
------------ ---------------------
           1                  2300
```

示例（存算一体分布式集群部署）

```sql
-- 按年份对销售额进行汇总，HAVING后指定相同聚集函数
SELECT 1,year,month,CONCAT(year,month),SUM(amount)
FROM sales_info
HAVING SUM(amount)>300
GROUP BY year,month;
           1 YEAR  MONTH CONCAT(YEAR,MONTH) SUM(AMOUNT)
------------ ----- ----- ------------------ -----------
           1 2001  01    200101                     500
           1 2021  05    202105                     600
 
SELECT 1,year,month,CONCAT(year,month),SUM(amount)
FROM sales_info
GROUP BY year,month
HAVING SUM(amount)>500;
           1 YEAR  MONTH CONCAT(YEAR,MONTH)           SUM(AMOUNT)
------------ ----- ----- ------------------ ---------------------
           1 2021  05    202105                               600
 
-- HAVING定义分组列的条件
SELECT 1,year,month,CONCAT(year,month),SUM(amount)
FROM sales_info
GROUP BY year,month
HAVING year>'2015';
           1 YEAR  MONTH CONCAT(YEAR,MONTH)           SUM(AMOUNT)
------------ ----- ----- ------------------ ---------------------
           1 2021  05    202105                               600
           1 2021  10    202110                               300
 
-- 单独使用HAVING语句，定义对整个查询结果的聚集约束条件,此时不能出现分组列
SELECT 1,SUM(amount)
FROM sales_info
HAVING SUM(amount)>300;
           1           SUM(AMOUNT)
------------ ---------------------
           1                  2300
```

<span id="rollupcubeclause" name="rollupcubeclause"></span>

###### rollup_cube_clause

**ROLLUP**

该关键字用于指定ROLLUP执行算子，该算子用于拓展GROUP BY聚集功能，区别在于GROUP BY仅返回每个分组的结果，指定了ROLLUP关键字会返回总计和每个分组的结果。

其等价于对ROLLUP后指定的列字段的每个层次级别创建grouping set，如`ROLLUP(A,B,C) == GROUPING SETS((A, B, C), (A, B), (A), ())`。

投影列上的常量表达式不会被当作分组键。

示例

```sql
select year,product,salsperson,sum(quantity) from sales_info group by rollup(year,product,salsperson);

YEAR  PRODUCT   SALSPERSON    SUM(QUANTITY) 
----- --------- ------------- ------------- 
2000  11001                              20
2001  11001     0201010011               30
2015  11001                              40
2021  11001                              60
2000  11001                              20
2001  11001                              30
2015  11001                              40
2021  11001                              60
2000                                     20
2001                                     30
2015                                     40
2021                                     60
                                        150

13 rows fetched.

select 1, 2, 3 from dual group by rollup (1, 2);

           1            2            3
------------ ------------ ------------
           1            2            3
           1            2            3
           1            2            3

3 rows fetched.
```

**CUBE**

该关键字用于指定CUBE执行算子，该算子用于拓展GROUP BY聚集功能，区别在于GROUP BY仅返回每个分组的结果，指定了CUBE关键字会返回所有组合的结果，其中包括每个分组的结果。

其等价于对CUBE后指定的列字段的所有组合创建grouping set，如`CUBE(A,B,C) ==  GROUPING SETS((A, B, C), (A, B), (A, C), (B, C), (A), (B), (C), ())`。

投影列上的常量表达式不会被当作分组键。

示例

```sql
select year,product,salsperson,sum(quantity) from sales_info group by cube(year,product,salsperson);

YEAR  PRODUCT   SALSPERSON    SUM(QUANTITY) 
----- --------- ------------- ------------- 
2000  11001                              20
2001  11001     0201010011               30
2015  11001                              40
2021  11001                              60
2000  11001                              20
2001  11001                              30
2015  11001                              40
2021  11001                              60
      11001                             120
      11001     0201010011               30
      11001                             150
2000                                     20
2001            0201010011               30
2015                                     40
2021                                     60
2000                                     20
2001                                     30
2015                                     40
2021                                     60
                                        150
                                        120
                0201010011               30

22 rows fetched.

select 1, 2, 3 from dual group by cube (1, 2);

           1            2            3
------------ ------------ ------------
           1            2            3
           1            2            3
           1            2            3
           1            2            3

4 rows fetched.
```

<span id="groupingsetsclause" name="groupingsetsclause"></span>

###### grouping_sets_clause

该语句用于指定GROUP BY的分组规则，并将结果聚合，等价于对指定组合执行GROUP BY分组，然后通过UNION ALL将结果联合起来。

投影列上的常量表达式不会被当作分组键。

示例

```sql
select year,product,salsperson,sum(quantity) from sales_info group by grouping sets(year,product,salsperson);

YEAR  PRODUCT   SALSPERSON    SUM(QUANTITY) 
----- --------- ------------- ------------- 
                                        120
                0201010011               30
2000                                     20
2001                                     30
2015                                     40
2021                                     60
      11001                             150

7 rows fetched.

select 1, 2, 3 from dual group by grouping sets (1, 2);

           1            2            3
------------ ------------ ------------
           1            2            3
           1            2            3

2 rows fetched.
```

<span id="setoper" name="setoper"></span>

#### set_oper

集合操作，用于将两个或两个以上的查询结果集组合成单个结果集，包括如下类型：

- **UNION**：合并且过滤掉重复值。
- **UNION ALL**：合并且保留重复值。
- **INTERSECT**：比较两个查询的结果，选择所有查询结果集中相同行的记录，且过滤掉重复值。
- **INTERSECT ALL**：比较两个查询的结果，选择所有查询结果集中相同行的记录，且保留重复值。
- **MINUS/EXCEPT**：比较两个查询的结果，返回在第一个查询结果集中，但不是第二个查询结果集中的行的记录，且过滤掉重复值。
- **MINUS ALL/EXCEPT ALL**：比较两个查询的结果，返回在第一个查询结果集中，但不是第二个查询结果集中的行的记录，且保留重复值。

集合操作中MINUS与EXCEPT完全等价。

进行集合操作的查询结果集的数据类型必须处于下面同一个分类中，否则函数返回Query column mismatch错误：

*   数值型：按优先度从高到低为DOUBLE、FLOAT、NUMBER、BIGINT、INT、SMALLINT、TINYINT，但BIT类型只能与同类型在同一个操作列表中。
*   字符型：按优先度从高到低为VARCHAR、CHAR。
*   日期时间型：按优先度从高到低为TIMESTAMP WITH TIME ZONE、TIMESTAMP WITH LOCAL TIME ZONE、TIMESTAMP、DATE、TIME，但INTERVAL YEAR TO MONTH、INTERVAL DAY TO SECOND只能与同类型在同一个参数列表中。
*   布尔型：BOOLEAN。
*   其他类型：RAW。

> **Note**: 
>
> 1.同一分类中数据类型不同时，低优先度类型向高优先度类型转换。
>
> 2.结果集为常量NULL（查询列为常量NULL所生成的结果集）时，不受上述分类限制。
>
> 3.所有集合运算为统一运算优先级，运算顺序以书写顺序为准，可通过双括号（）调整想要达到的运算优先级。

示例

```sql
-- UNION
SELECT area_no,area_name,DHQ
FROM (SELECT area_no,area_name,DHQ FROM area WHERE area_no IN ('01','02')
      UNION
      SELECT area_no,area_name,DHQ FROM area WHERE area_no IN ('01','03')
     );
AREA_NO AREA_NAME          DHQ      
------- ------------------ ----------
01      EastChina             Shanghai   
02      WestChina             Chengdu    
03      SouthChina             Guangzhou  
  
-- UNION ALL
SELECT area_no,area_name,DHQ
FROM (SELECT area_no,area_name,DHQ FROM area WHERE area_no IN ('01','02')
      UNION ALL
      SELECT area_no,area_name,DHQ FROM area WHERE area_no IN ('01','03')
     );
AREA_NO AREA_NAME          DHQ      
------- ------------------ ----------
01      EastChina             Shanghai   
03      SouthChina             Guangzhou  
01      EastChina             Shanghai   
02      WestChina             Chengdu

-- INTERSECT
SELECT area_no,area_name,DHQ
FROM (SELECT area_no,area_name,DHQ FROM area WHERE area_no IN ('01','02')
      INTERSECT
      SELECT area_no,area_name,DHQ FROM area WHERE area_no IN ('01','03')
     );
AREA_NO AREA_NAME          DHQ      
------- ------------------ ----------
01      EastChina             Shanghai   
  
-- INTERSECT ALL
SELECT area_no,area_name,DHQ
FROM (SELECT area_no,area_name,DHQ FROM area WHERE area_no IN ('01','02')
      INTERSECT ALL
      SELECT area_no,area_name,DHQ FROM area WHERE area_no IN ('01','03')
     );
AREA_NO AREA_NAME          DHQ      
------- ------------------ ----------
01      EastChina             Shanghai   

-- MINUS
SELECT area_no,area_name,DHQ
FROM (SELECT area_no,area_name,DHQ FROM area WHERE area_no IN ('01','02')
      MINUS
      SELECT area_no,area_name,DHQ FROM area WHERE area_no IN ('01','03')
     );
AREA_NO AREA_NAME          DHQ      
------- ------------------ ----------
02      WestChina             Chengdu  
  
-- MINUS ALL
SELECT area_no,area_name,DHQ
FROM (SELECT area_no,area_name,DHQ FROM area WHERE area_no IN ('01','02')
      MINUS ALL
      SELECT area_no,area_name,DHQ FROM area WHERE area_no IN ('01','03')
     );
AREA_NO AREA_NAME          DHQ      
------- ------------------ ----------
02      WestChina             Chengdu   
```

<span id="orderbyclause" name="orderbyclause"></span>

#### order\_by\_clause

该语句用于对查询结果集按照排序键进行排序。

排序键可以为：

*   expr：表达式
*   position：select\_list中的列项位置，默认按此项排序
*   c\_alias：select\_list中为列项定义的别名

排序键不允许为如下类型：

- CLOB
- BLOB
- NCLOB
- CURSOR
- JSON
- UDT
- XMLTYPE
- BFILE

当排序键expr为[子查询](../基本SQL元素/子查询)时，需遵顼如下规则：

- 只能为标量子查询，即返回单行单列（标量值）的子查询。

- 查询LSC表和TAC表时，排序键只能为非关联子查询。

##### NULLS (FIRST|LAST)

对于空值，用NULLS FIRST|NULLS LAST语句来指定空值排列在最前或最后。当未指定此语句时，对升序排列缺省为NULLS LAST，对降序排列缺省为NULLS FIRST。

执行`ORDER BY`SQL语句时，如果排序字段上创建了索引，通过索引扫描免除排序可以提高查询效率。

- yashan模式默认NULL值在后，通过NULLS LAST免除排序；
- mysql模式默认NULL值在前，通过NULLS FIRST免除排序。

##### ASC|DESC

排序方式可指定为升序（ASC）或降序（DESC），未指定则缺省为升序。

示例

```sql
SELECT year,branch,sum(amount) FROM sales_info GROUP BY year,branch ORDER BY sum(amount)/sum(quantity);
YEAR  BRANCH           SUM(AMOUNT)
----- ------ ---------------------
2015  0101                     300
2015  0102                     300
2000  0102                     300
2021  0101                     900
2001  0201                     500
 
SELECT year,branch,sum(amount) FROM sales_info GROUP BY year,branch ORDER BY 2;
YEAR  BRANCH           SUM(AMOUNT)
----- ------ ---------------------
2015  0101                     300
2021  0101                     900
2015  0102                     300
2000  0102                     300
2001  0201                     500
```

<span id="rowlimitingclause" name="rowlimitingclause"></span>

#### row\_limiting\_clause

该语句用于抓取查询结果集的指定行。

##### LIMIT

指定抓取的行数。LIMIT满足如下规则：

*   LIMIT可以为一个表达式，表达式的结果必须为一个数字。
*   查询HEAP表时，LIMIT可以使用标量子查询和绑定参数。
*   如果LIMIT是负值，则将其视为0。
*   如果LIMIT是NULL，则不返回任何行。 
*   如果LIMIT大于查询的行数，则返回所有行。
*   如果LIMIT是小数，则小数部分被截断。

##### OFFSET

指定抓取的起始行偏离数，缺省为第一行的偏离数0。OFFSET满足如下规则：

*   OFFSET可以为一个表达式，表达式的结果必须为一个数字。
*   查询HEAP表时，OFFSET可以使用标量子查询和绑定参数。
*   如果OFFSET是负值，则将其视为0。 
*   如果OFFSET是NULL，或者大于查询的行数，则不返回任何行。
*   如果OFFSET是小数，则小数部分被截断。

执行本语句将对优化器产生以下影响：

- 如果SQL中同时出现了order_by_clause和row_limiting_clause，优化器会将排序计划改写成ORDER BY STOPKEY计划。
- 如果SQL中同时出现了group_by_clause、distinct、order_by_clause、以及row_limiting_clause，优化器将基于cost可能生成SORT GROUP BY STOPKEY/SORT DISTINCT STOPKEY计划。

示例

```sql
-- 从branches表的第一行开始抓取前四行数据
SELECT branch_no,branch_name,area_no,address FROM branches LIMIT 4;
BRANCH_NO BRANCH_NAME         AREA_NO ADDRESS         
--------- ------------------- ------- -----------------
0001      Shenzhen                                        
0101      Shanghai              01      Jingan District, Shanghai      
0102      Nanjing              01      City of Nanjing   
0103      Fuzhou              01                        
 
-- 从branches表的第四行开始抓取前四行数据 
SELECT branch_no,branch_name,area_no,address FROM branches LIMIT 4 OFFSET 3;
BRANCH_NO BRANCH_NAME         AREA_NO ADDRESS         
--------- ------------------- ------- -----------------
0103      Fuzhou              01                        
0104      Xiamen              01      Xiamen            
0401      Beijing              04                        
0402      Tianjin              04
```

<span id="offsetfetchclause" name="offsetfetchclause"></span>

#### offset\_fetch\_clause

OFFSET FETCH的功能实现和LIMIT OFFSET完全一致，用于抓取查询结果集的指定行。

##### ROWCOUNT

指定抓取的行数。ROWCOUNT满足如下规则：

*   ROWCOUNT可以为一个表达式，表达式的结果必须为一个数字。
*   查询HEAP表时，LIMIT可以使用标量子查询和绑定参数。
*   如果ROWCOUNT是负值，则将其视为0。
*   如果ROWCOUNT是NULL，则不返回任何行。
*   如果ROWCOUNT大于查询的行数，则返回所有行。
*   如果ROWCOUNT是小数，则小数部分被截断。

##### OFFSET

指定抓取的起始行偏离数，缺省为第一行的偏离数0。OFFSET满足如下规则：

*   OFFSET可以为一个表达式，表达式的结果必须为一个数字。
*   查询HEAP表时，OFFSET可以使用标量子查询和绑定参数。
*   如果OFFSET是负值，则将其视为0。
*   如果OFFSET是NULL，或者大于查询的行数，则不返回任何行。
*   如果OFFSET是小数，则小数部分被截断。

##### EXACT/APPROX/APPROXIMATE

在向量数据查询场景，指定本次查询为向量数值的精确查询还是近似查询。

- EXACT：精确查询，缺省时表示精确查询。
- APPROX：近似查询，按照设置的距离函数计算并返回与输入向量最相似的向量。
- APPROXIMATE：APPROX的同义词。

示例

```sql
-- 从branches表的第一行开始抓取前四行数据
SELECT branch_no,branch_name,area_no,address FROM branches FETCH FIRST 4 ROW ONLY;
BRANCH_NO BRANCH_NAME         AREA_NO ADDRESS         
--------- ------------------- ------- -----------------
0001      Shenzhen                                    
0101      Shanghai              01      Jingan District, Shanghai      
0102      Nanjing              01      City of Nanjing   
0103      Fuzhou              01                        
 
-- 从branches表的第四行开始抓取前四行数据 
SELECT branch_no,branch_name,area_no,address FROM branches OFFSET 3 ROW FETCH FIRST 4 ROW ONLY;
BRANCH_NO BRANCH_NAME         AREA_NO ADDRESS         
--------- ------------------- ------- -----------------
0103      Fuzhou              01                        
0104      Xiamen              01      Xiamen            
0401      Beijing              04                        
0402      Tianjin              04
```

<span id="forupdateclause" name="forupdateclause"></span>

### for\_update\_clause

该语句用于将查询结果集对应的行记录锁定，锁定后其他用户将只能对这些行进行查询，而不能执行锁定或修改等操作，直至当前事务结束。

只能在顶层的SELECT语句中指定FOR UPDATE，子查询和CTE不能指定FOR UPDATE。

本语句不可与DISTINCT，GROUP BY，FLASHBACK，聚集函数，游标表达式等组合使用。

#### OF column\_name

通过指定具体的列字段名来锁定列所在的表，而无需将FROM后出现的所有表都锁定。常用于多表连接查询时仅对部分特定的表（即OF column\_name指定列对应的表）执行更新。省略时，将锁定查询涉及到的所有表对象对应的记录。

通过column\_name标识出其所在的表对象，必须是一个真实的列字段名称，不能为别名。

可联合表、视图、同义词，或者在FROM语句中定义的别名，来指定列字段名。

#### WAIT|NOWAIT|SKIP LOCKED

指定当要锁定的记录里至少有部分行正被其他用户锁定时的操作，缺省为一直等待到那些行被解锁后，再执行操作并返回。

*   WAIT ntimes：等待指定的时间，ntimes的单位为秒，超过此时间后报错。
*   NOWAIT：不等待且不执行操作，直接返回。
*   SKIP LOCKED：跳过被锁定的行，继续执行其他的行。不适用于LSC表的稳态数据。

本语句用来定义行锁时的操作，当出现表锁时，直接进入表锁等待。

示例（单机HEAP表、TAC表）

```sql
SELECT TRIM(address) address FROM branches FOR UPDATE;
 
SELECT * FROM area a,branches b WHERE a.area_no=b.area_no AND a.area_no='01' FOR UPDATE OF a.DHQ;         -- 此时仅锁定area表中的对应行
 
SELECT TRIM(address) address FROM branches FOR UPDATE NOWAIT;
 
SELECT TRIM(address) address FROM branches FOR UPDATE WAIT 10;
 
SELECT TRIM(address) address FROM branches FOR UPDATE SKIP LOCKED;
```

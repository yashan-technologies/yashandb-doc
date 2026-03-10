General Description
----

SELECT is used to execute query operations on tables, views, and AC in the database.

YashanDB supports a rich variety of query operations, including but not limited to:

- Multi-table joins ([subquery](#subquery), [SET](#setoper), [JOIN](#joinclause), etc.)
- Sorting [ORDER BY](#orderbyclause)
- Grouping [GROUP BY](#groupbyclause)
- [CASE](#caseclause)
- [LIMIT](#rowlimitingclause)
- [CTE](#cteclause)
- [Hierarchical/Recursive](#hierarchicalqueryclause)
- [Sampling](#sampleclause)
- [Specify partition](#queryname)
- [Specify slice](#queryslice) (Slice)
- [Flashback Query](#flashbackqueryclause)

Among these, basic queries such as multi-table joins, sorting, and grouping can be performed on AC.

In ISC Distributed Cluster Deployment, operations related to [global dynamic views](../../All Manuals/Reference Manual/System Views/Dynamic Performance Views/00Dynamic Performance Views) are executed locally at each node and are finally aggregated to CN.

Statement Definition
----

**select::=**

```ebnf+diagram
syntax::= subquery [for_update_clause]
```

**[subquery](#subquery)::=**

```ebnf+diagram
syntax::= (query_block
|(subquery (set_oper subquery) {" " (set_oper subquery)})
|"(" subquery ")")
[order_by_clause]
[row_limiting_clause|offset_fetch_clause]
```

**[query\_block](#queryblock)::=**

```ebnf+diagram
syntax::= [with_clause]
SELECT [hint] [DISTINCT] select_list
FROM (table_reference|join_clause|"(" join_clause ")")
{" " (table_reference|join_clause|"(" join_clause ")")}
[where_clause]
[hierarchical_query_clause]
[group_by_clause]
```

**[with\_clause](#withclause)::=**

```ebnf+diagram
syntax::= WITH (cte_clause) {"," (cte_clause)}
```

**[cte\_clause](#cteclause)::=**

```ebnf+diagram
syntax::= cte_name ["(" (column_alias) {"," (column_alias)} ")"] AS "(" subquery ")" 
```

**[select\_list](#selectlist)::=**

```ebnf+diagram
syntax::= ("*"|expr_clause [[AS] alias]) {"," ("*"|expr_clause [[AS] alias])} 
```

**[expr\_clause](#exprclause)::=**

```ebnf+diagram
syntax::= ["("] (query_block|expr|case_clause|udt_expr) [")"]
```

**[case\_clause](#caseclause)::=**

```ebnf+diagram
syntax::= CASE (simple_case_expression|searched_case_expression)
{" " (simple_case_expression|searched_case_expression)}
[else_clause] END
```

**[simple\_case\_expression](#SimpleCase)::=**

```ebnf+diagram
syntax::= expr (WHEN comparison_expr THEN return_expr)
{" " (WHEN comparison_expr THEN return_expr)}
```

**[searched\_case\_expression](#SimpleCase)::=**

```ebnf+diagram
syntax::= (WHEN condition THEN return_expr) {" " (WHEN condition THEN return_expr)}
```

**[else\_clause](#SimpleCase)::=**

```ebnf+diagram
syntax::= ELSE else_expr
```

**[table\_reference](#tablereference)::=**

```ebnf+diagram
syntax::= ((query_name|subquery)[sample_clause][flashback_query_clause][t_alias][pivot_clause|unpivot_clause]) {"," ((query_name|subquery) [sample_clause][flashback_query_clause][t_alias][pivot_clause|unpivot_clause])}
```

**[query_name](#queryname)::=**

```ebnf+diagram
syntax::= [schema "."]  (((table_name[dblink]|synonym_name)[partition_extension_clause][SLICE "(" slice_id ")" ]  )
|view_name
|table_collection_expression)
[sample_clause]
[pivot_clause|unpivot_clause]
```

**[partition\_extension\_clause](#partitionextensionclause)::=**

```ebnf+diagram
syntax::= PARTITION ("(" partition ")"|FOR "(" (partition_key_value) {","(partition_key_value)} ")")|SUBPARTITION ("(" subpartition ")"|FOR "(" (subpartition_key_value) {","(subpartition_key_value)} ")")
```

**[table_collection_expression](#tablecollectionexpression)::=**

```ebnf+diagram
syntax::= TABLE "(" collection_expression ")"
```

**[sample_clause](#sampleclause)::=**

```ebnf+diagram
syntax::= SAMPLE "(" sample_percent ")" [SEED "(" seed_value ")"]
```

**[flashback\_query\_clause](#flashbackqueryclause)::=**

```ebnf+diagram
syntax::= AS OF ((SCN|TIMESTAMP) expr) 
```

**[pivot\_clause](#pivotclause)::=**

```ebnf+diagram
syntax::= PIVOT "(" aggregate_expression [[AS] alias] { "," aggregate_expression [[AS] alias]} FOR column_expression IN "(" const_expression [[AS] alias] { "," const_expression [[AS] alias]}  ")" ")" 
```

**[unpivot\_clause](#unpivotclause)::=**

```ebnf+diagram
syntax::= UNPIVOT[( INCLUDE | EXCLUDE) NULLS ] "(" value_column FOR pivot_column IN "(" column [AS alias ] { "," column [AS alias]} ")" ")" 
```

**[join\_clause](#joinclause)::=**

```ebnf+diagram
syntax::= table_reference (inner_cross_join_clause|outer_join_clause )
{" " (inner_cross_join_clause|outer_join_clause)}
```

**[inner\_cross\_join\_clause](#innercrossjoinclause)::=**

```ebnf+diagram
syntax::= ([INNER] JOIN table_reference ON condition)|(CROSS JOIN table_reference)
```

**[outer\_join\_clause](#outerjoinclause)::=**

```ebnf+diagram
syntax::= outer_join_type JOIN table_reference [ON condition]
```

**[outer\_join\_type](#outerjointype)::=**

```ebnf+diagram
syntax::= (LEFT|RIGHT|FULL) [OUTER]
```

**[where\_clause](#whereclause)::=**

```ebnf+diagram
syntax::= WHERE condition
```

**[hierarchical\_query\_clause](#hierarchicalqueryclause)::=**

```ebnf+diagram
syntax::= ((([connect_by_clause]|[start_with_clause]|[group_by_clause])
{" " ([connect_by_clause]|[start_with_clause]|[group_by_clause])})
[order_siblings_by_clause])
```

**[connect\_by\_clause](#connectbyclause)::=**

```ebnf+diagram
syntax::= CONNECT BY [NOCYCLE] condition
```

**[start\_with\_clause](#startwithclause)::=**

```ebnf+diagram
syntax::= START WITH condition
```

**[order\_siblings_by\_clause](#ordersiblingsbyclause)::=**

```ebnf+diagram
syntax::= ORDER SIBLINGS BY ((expr|position|c_alias) [ASC|DESC] [(NULLS FIRST)|(NULLS LAST)])
{"," ((expr|position|c_alias) [ASC|DESC] [(NULLS FIRST)|(NULLS LAST)])}
```

**[group\_by\_clause](#groupbyclause)::=**

```ebnf+diagram
syntax::= (group_by_clause1|group_by_clause2) 
```

**[group\_by\_clause1](#groupbyclause)::=**

```ebnf+diagram
syntax::= GROUP BY (((expr)){"," (expr)}|rollup_cube_clause|grouping_sets_clause) [HAVING condition]
```

**[rollup\_cube\_clause](#rollupcubeclause)::=**

```ebnf+diagram
syntax::= (ROLLUP|CUBE) "("((expr)){"," (expr)}")"
```

**[grouping_sets_clause](#groupingsetsclause)::=**

```ebnf+diagram
syntax::= GROUPING SETS "("((expr)){"," (expr)}")"
```

**[group\_by\_clause2](#groupbyclause)::=**

```ebnf+diagram
syntax::= [HAVING condition] [GROUP BY] (((expr)){"," (expr)}|rollup_cube_clause|grouping_sets_clause)
```

**[set_oper](#setoper)::=**

```ebnf+diagram
syntax::= (UNION|INTERSECT|MINUS|EXCEPT) [ALL]
```

**[order\_by\_clause](#orderbyclause)::=**

```ebnf+diagram
syntax::= ORDER BY ((expr|position|c_alias) [ASC|DESC] [(NULLS FIRST)|(NULLS LAST)]) 
{"," ((expr|position|c_alias) [ASC|DESC] [(NULLS FIRST)|(NULLS LAST)])}
```

**[row\_limiting\_clause](#rowlimitingclause)::=**

```ebnf+diagram
syntax::= [LIMIT expr [offset expr]]
```

**[offset\_fetch\_clause](#offsetfetchclause)::=**

```ebnf+diagram
syntax::= [OFFSET offset (ROW | ROWS)] [ FETCH (FIRST | NEXT) rowcount (ROW | ROWS) ONLY]
```

**[for\_update\_clause](#forupdateclause)::=**

```ebnf+diagram
syntax::= FOR UPDATE [OF [([schema "."] (table_name|view_name|synonym_name) ".")|t_alias] column_name]
[(NOWAIT)|(WAIT ntimes)|(SKIP LOCKED)]
```

<span id="subquery" name="subquery" class="yaslink"></span>

### 1. subquery

YashanDB's `SELECT` statement supports nested queries (performing a `SELECT` query on the results of a `SELECT` statement enclosed in `()`) and combining queries (using set operations to combine the results of multiple `SELECT` statements). Different query methods can be interleaved and used in multiple layers. For example:

```sql
SELECT * FROM (SELECT a,b FROM (SELECT cl_a a,cl_b b FROM table_a WHERE ...
               					UNION ALL
               					SELECT cl_c a,cl_d b FROM table_b WHERE ...
                               ) 
               WHERE ...
               )
WHERE ...
```

Each complete SELECT query statement (including its sub-queries) can be referred to as a subquery (`subQuery`).

For the `select_list` items in a subquery:

-  If these items explicitly specify aliases (for example, defining an alias `a` for column `c1_a`), the system uses this alias as the column name of the result set returned by the subquery. YashanDB limits the length of explicitly specified aliases to 64 bytes.
- If these items do not explicitly specify aliases, the system defaults to using the item column name as the column name of the result set returned by the subquery, truncated to 20 bytes. In this case, if there are columns within the `select_list` of the subquery with the same name for the first 20 bytes, it will lead to a result set with duplicate column names, triggering a system error.

***Example***

```sql
SELECT * FROM (SELECT
                 1111111111111111111111111111111111111111111111111111111111111111,
                 11111111111111111111111111111111111111111111111111111111111111112
               FROM dual);

[3:1]YAS-04301 ambiguous column
```

<span id="queryblock" name="queryblock" class="yaslink"></span>

#### 1.1. query\_block

A query statement containing a single `SELECT` is referred to as a query block (`queryBlock`).

<span id="withclause" name="withclause" class="yaslink"></span>

##### 1.1.1. with\_clause

This statement is used to define a Common Table Expression (CTE). When a certain subquery is used multiple times, you can directly use the CTE name without needing to rewrite that `SELECT` statement.

<span id="cteclause" name="cteclause" class="yaslink"></span>

###### 1.1.1.1. cte\_clause

Definition of CTE includes:

* `cte_name`: Name, used as an identifier for reference elsewhere.
* `column_alias`: Defines the columns of the CTE, where column fields should come from the columns defined in the subsequent subquery.
* `subquery`: A subquery used to create a CTE, where the [pivot_clause](#pivotclause) is not allowed.

***Example***

```sql
-- The WITH statement is used to retrieve codes and names for areas '01' and '04' from the area table. In the later query to obtain related area name data from the branches table, the previously written statement does not need to be repeated; it can directly associate with q_area.
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
```

##### 1.1.2. hint

This statement is used to suggest a given plan to the optimizer, so that it generates the execution plan for the statement according to this plan. For details, please refer to [hint](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/hint).

##### 1.1.3. distinct

This statement is used to filter the query results, returning only one record for any duplicated records found in the results.

<span id="selectlist" name="selectlist" class="yaslink"></span>

##### 1.1.4. select\_list

This statement is used to specify the columns to query in the query statement, such as a specific column field from a table after the `FROM` clause, etc. Multiple items are separated by commas. `*` indicates all columns from all tables/views/AC/subqueries listed after `FROM`.

`AS alias` is used to define an alias, and `AS` can be omitted.

<span id="exprclause" name="exprclause" class="yaslink"></span>

###### 1.1.4.1. expr\_clause

Specifies specific column items, which can be:

* `query_block`: Using the result of a single query block as a column item
* [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr): An expression
* CASE statement
* `udt_expr`: For Object UDT columns, the format of `udt_expr` is `table_alias.attribute_name`; for Varray UDT and Nested Table UDT columns, the format is `table_alias.*`. For detailed usage methods and examples, see [User UDT](../../All Manuals/Development Guide/SQL Reference Manual/Data Types (yashan Mode)/User-Defined Types).

***Example*** for Standalone Deployment

```sql
SELECT SYSDATE querying_date,
a.branch_name branch,
b.area_name area,
(SELECT DHQ FROM area WHERE area_no='10') default_DHQ
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

***Example*** for ISC Distributed Cluster Deployment

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

<span id="caseclause" name="caseclause" class="yaslink"></span>

**case_clause**

The CASE expression is analogous to IF ELSE in programming languages and can be written in two forms: Simple Case and Searched Case.

<span id="SimpleCase" name="SimpleCase" class="yaslink"></span>

simple_case_expression = expr { WHEN comparison_expr THEN return_expr }.

searched_case_expression = { WHEN condition THEN return_expr }.

else_clause = ELSE else_expr.

In the Simple Case form, the system searches for the first `comparison_expr` that equals `expr` and returns the corresponding `return_expr`. If there is no equal `comparison_expr` and an ELSE clause exists, it returns `else_expr`. Otherwise, it returns NULL.

In the Searched Case form, the system checks each `condition` from left to right and returns the first corresponding `return expr`. If all conditions are FALSE and an ELSE clause exists, it returns `else_expr`. Otherwise, it returns NULL.

All expressions in the CASE statement (else_expr/return_expr/expr/comparison_expr, prefix names are for distinction) are general expressions, and their values must all belong to one of the following major categories within the same statement:

* Numeric types, ordered from lowest to highest priority: TINYINT, SMALLINT, INT, BIGINT, NUMBER, FLOAT, DOUBLE.
* Character types, ordered from lowest to highest priority: CHAR, VARCHAR.
* Date and time types, ordered from lowest to highest priority: DATE, TIMESTAMP.
* Other data types are classified into one major category.

Within each major category, the values of the expressions are unified to the highest priority data type before matching or returning. Therefore, the data type returned by the CASE expression is the highest priority type of all expressions.

***Example***

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
 
-- If there are results for conditions not listed, return NULL                                                          
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
 
-- For all cases not listed, give a default value
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

<span id="tablereference" name="tablereference" class="yaslink"></span>

##### 1.1.5. table\_reference

This statement is used to specify the definition of the query object in the `FROM` clause.

YashanDB supports defining aliases for query objects, which can be of the following types:

* [query_name](#queryname): Tables, views, synonyms, partitions, subpartitions, AC, etc.
* [subquery](#query_subquery): The result of a subquery.
* [dblink](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/dblink): Remote tables.

Query objects cannot simultaneously contain heap tables and LSC tables, otherwise an error will be returned.

<span id="queryname" name="queryname" class="yaslink"></span>

###### 1.1.5.1. query\_name

This statement is used to specify the query objects such as tables, views, synonyms, partitions, subpartitions, AC, etc.

<span id="partitionextensionclause" name="partitionextensionclause" class="yaslink"></span>

**partition_extension_clause**

The specification method for table partition and subpartition includes:

-  By name: Directly retrieving partition or subpartition objects.
- By provided key value (Key Value): Comparing the key value with the partition boundary value of the table to compute the partition or subpartition object; the key value can be any value within the boundary range. The number of key values must correspond with the number of partition columns; in specifying subpartitions, the number must correspond to both partition and subpartition columns, with multiple items separated by commas.

> **Note**: 
>
> In the distributed example table, if no partition name is specified, the database will define partition names and subpartition names, which can be viewed in DBA_TAB_PARTITIONS/DBA_TAB_SUBPARTITIONS for partition and subpartition information.

***Example***

```sql
-- Execute the following statement to query partition names
SELECT partition_name
FROM DBA_TAB_PARTITIONS
WHERE table_name='SALES_INFO';
PARTITION_NAME
----------------------------------------------------------------
P_SALES_INFO_1
P_SALES_INFO_2
P_SALES_INFO_3

-- sales_info is a partitioned table; specific partitions can be queried to retrieve its data
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

-- Retrieve the names of subpartitions for the sales_info table
SELECT partition_name,subpartition_name 
FROM DBA_TAB_SUBPARTITIONS
WHERE table_name='SALES_INFO';
-- The following output is an example for a standalone
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

-- Choose one of the subpartitions to specify querying data
SELECT year,month,branch,product,quantity,amount,salsperson FROM sales_info SUBPARTITION (P_SALES_INFO_1_SP_SALES_INFO_2);
YEAR  MONTH BRANCH PRODUCT      QUANTITY      AMOUNT SALSPERSON
----- ----- ------ --------- ----------- ----------- -------------
2021  10    0402   11001              20         300
```

<span id="queryslice" name="queryslice" class="yaslink"></span>

**Specify Slice Query**

For LSC tables, querying a specific slice's data by specifying the Slice ID is supported, with the following rules:

-  Slice query can only be performed on LSC tables; otherwise, an error will be returned.
- For partitioned tables, if slice is specified, partition must also be specified; otherwise, an error will be returned.
- Slice ID of 0 indicates querying data from the mutable data area (MCOL).
- Querying the `V$LSC_SLICE_STAT` view can provide all Slice IDs included in a specific LSC table (partition).

***Example*** for LSC tables

```sql
-- In the partition of orders_info table, the following stable data exists
SELECT order_no,product_no,area,branch,order_date,salesperson,id FROM orders_info PARTITION(p_orders_info_1);
ORDER_NO          PRODUCT_NO AREA  BRANCH ORDER_DATE            SALESPERSON            ID
----------------- ---------- ----- ------ --------------------- ------------- -----------
20010102020001    11001      01    0101   2023-04-05            0201010011            300
20210102020002    11001      04    0401   2023-04-05            0201008003            400
20210102020002    11002      05    0501   2023-04-05            0201010011            200
20210102020002    10001      01    0102   2023-04-05            0201008003            100

-- A new record is inserted into the orders_info table, stored in the mutable data area
INSERT INTO orders_info VALUES ('20210102020003','10002','03','0701',SYSDATE-400,'0201008003',900);

-- Querying the V$LSC_SLICE_STAT view to obtain Slice ID
SELECT utp.table_name, utp.partition_name, lss.slice_id
FROM USER_TAB_PARTITIONS utp, V$LSC_SLICE_STAT lss, USER_OBJECTS uo
WHERE utp.table_name = 'ORDERS_INFO'
AND uo.subobject_name = utp.partition_name
AND uo.object_id = lss.obj;
TABLE_NAME        PARTITION_NAME        SLICE_ID 
----------------- --------------------  ---------- 
ORDERS_INFO       P_ORDERS_INFO_1       0
ORDERS_INFO       SYS_P12               0
ORDERS_INFO       SYS_P13               0

-- Querying MCOL data
SELECT order_no,product_no,area,branch,order_date,salesperson,id FROM orders_info PARTITION(p_orders_info_1) SLICE (0);
ORDER_NO          PRODUCT_NO AREA  BRANCH ORDER_DATE            SALESPERSON            ID
----------------- ---------- ----- ------ --------------------- ------------- -----------
20010102020001    11001      01    0101   2023-04-05            0201010011            300
20210102020002    11001      04    0401   2023-04-05            0201008003            400
20210102020002    11002      05    0501   2023-04-05            0201010011            200
20210102020002    10001      01    0102   2023-04-05            0201008003            100
20210102020003    10002      03    0701   2023-04-05            0201008003            500
```

<span id="tablecollectionexpression" name="tablecollectionexpression" class="yaslink"></span>

**table_collection_expression**

This statement can be used to execute two situations:

- UDT
- Table function 

**UDT**

Can be used to query Varray UDT or Nested Table UDT data, for details refer to [User UDT](../../All Manuals/Development Guide/SQL Reference Manual/Data Types (yashan Mode)/User-Defined Types). 

When executing this type of query, the `collection_expression` is a Varray object or Nested Table object with the syntax of table alias.UDT column name.

The Varray object is dependent on the parent table, so make sure the execution plan accesses the main table first.

***Example*** for Heap tables

```sql
DROP TABLE IF EXISTS city_intro;
CREATE OR REPLACE TYPE brc_array AS VARRAY(20) OF CHAR(15);
/
CREATE OR REPLACE TYPE city_table AS TABLE OF CHAR(10);
/

-- Create a city_intro table containing Varray UDT and Nested Table UDT columns
CREATE TABLE city_intro (id INT, branches brc_array, citys city_table)
NESTED TABLE citys STORE AS nt_citys;

-- Insert data
INSERT INTO city_intro 
VALUES (1,
        brc_array('branch01','branch02','branch03'),
        city_table('shenzhen','guangzhou','dongguan'));
                  
-- Query data
SELECT /*+ LEADING(c) */ c.id, cbranch.*, ccity.*
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

**Table Function**

A table function can be used for queries, for specific descriptions of table functions, refer to the section describing the built-in table function in [Built-in Functions](../../All Manuals/Development Guide/SQL Reference Manual/内置函数（yashan模式）/00内置函数（yashan模式）).

When executing this type of query, `collection_expression` is the required table function.

<span id="query_subquery" name="query_subquery" class="yaslink"></span>

###### 1.1.5.2. subquery

This statement is used to specify the result of a subquery.

***Example*** for Standalone Deployment and YAC Deployment

```sql
-- Create a synonym for the sales_info table, and partition can also be specified to query its data
CREATE SYNONYM sy_sales_info FOR sales_info;
SELECT year,month,branch,product,quantity,amount,salsperson FROM sy_sales_info PARTITION(p_sales_info_1);
YEAR  MONTH BRANCH PRODUCT      QUANTITY      AMOUNT SALSPERSON
----- ----- ------ --------- ----------- ----------- -------------
2021  10    0402   11001              20         300
 
-- Used in a subquery
SELECT b.branch_name branch,
SUM(s.amount) salesamount
FROM branches b,
(SELECT * FROM sy_sales_info PARTITION(p_sales_info_2)) s
WHERE b.branch_no=s.branch
GROUP BY b.branch_name;
BRANCH                                                           SALESAMOUNT
---------------------------------------------------------------- ----------- 
Chengdu                                                                   500
Nanjing                                                                   600
```

<span id="sampleclause" name="sampleclause" class="yaslink"></span>

###### 1.1.5.3. sample\_clause

SAMPLE is used to randomly extract a sample from a table by proportion, and subsequent WHERE conditions will be based on the sample data instead of the entire table data.

This statement may apply to a single physical table or a view based on a single physical table. Using SAMPLE on a view with multi-table joins will return an error.

This statement cannot be used in ISC Distributed Cluster Deployment.

- **sample_percent**

  Specifies the percentage of the sample, which must be a constant value in the range [0.000001, 100] (other types that can be implicitly converted to numerical types are supported). This percentage represents the probability that each data row is selected as a sample during sampling; this probability is a statistical concept, meaning that the system will not precisely return the number of records based on the calculated `sample_percent`.

- **SEED seed_value**
  
  This statement defines a seed value; the system will attempt to return the same sample for the same seed value. If this statement is not specified, the samples returned by the system are random. The value of `seed_value` must be a constant integer in the range [0, 4294967295] (other types that can be implicitly converted to numerical types are supported, and decimal values are truncated to integers), with values greater than 4294967295 treated as 4294967295.

***Example*** for Heap tables

```sql
-- Randomly return samples
SELECT area_no,area_name,DHQ FROM area SAMPLE(60);
AREA_NO AREA_NAME                 DHQ                   
------- ------------------------- --------------------- 
03      SouthChina                    Guangzhou            
04      NorthChina                    Beijing              
05      CentralChina                    Wuhan  

-- Specify the same seed to return the same sample
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

<span id="flashbackqueryclause" name="flashbackqueryclause" class="yaslink"></span>

###### 1.1.5.4. flashback\_query\_clause

YashanDB supports querying recent historical data. For example, if a record has been changed by an UPDATE or DELETE operation at a certain point in time, the flashback query clause can retrieve the data values of that record before it was changed within a certain time range (dependent on the cleanup rules of the undo space).

You can use SCN numbers or TIMESTAMP labels to locate the desired row record for the flashback query.

The system uses Beijing time when recording TIMESTAMP.

This statement cannot be used in ISC Distributed Cluster Deployment.

***Example*** for Standalone Deployment and YAC Deployment

```sql
-- A record existing in the area table
SELECT area_no,area_name,DHQ FROM area WHERE area_no='03';
AREA_NO AREA_NAME          DHQ         
------- ------------------ ------------
03      SouthChina             Guangzhou       
 
-- Get current time
SELECT SYSTIMESTAMP res FROM dual;
RES                                            
----------------------------------------------------------------
2023-12-17 14:14:08.498126 +08:00    
 
-- Delete this record and commit
DELETE FROM area WHERE area_no='03';
COMMIT;
SELECT area_no,area_name,DHQ FROM area WHERE area_no='03';
AREA_NO AREA_NAME          DHQ         
------- ------------------ ------------
 
 
-- Using flashback to query back the historical data snapshot
SELECT area_no,area_name,DHQ FROM area AS OF TIMESTAMP TIMESTAMP('2023-12-17 14:14:08.498126')
WHERE area_no='03';
AREA_NO AREA_NAME          DHQ         
------- ------------------ ------------
03      SouthChina             Guangzhou     
```

<span id="pivotclause" name="pivotclause" class="yaslink"></span>

###### 1.1.5.5. pivot\_clause

PIVOT is used for row-column transformation. It extracts the columns after the FOR statement from the result set and, based on the list of constant expressions specified in the IN clause, forms new projection columns and outputs the corresponding aggregate results of the respective aggregate function.

PIVOT can operate on any result set (whether it be a single table query or a result of multiple table joins).

If the preceding result set from the pivot clause involves multiple tables, only explicit join using JOIN ON is allowed; implicit join using commas is not permitted.

If a column name after IN exceeds 64 characters, the output column name will automatically be truncated beyond 64 characters.

- **aggregate_expression**

  The aggregation columns in `aggregate_expression` must appear in the projection columns of the query. 

  Aggregate functions used in aggregate_expression are not allowed to include GROUPING_ID, GROUP_ID, GROUPING functions, PERCENTILE_CONT function, MEDIAN function and GROUP_CONCAT-related functions (GROUP_CONCAT, WM_CONCAT, LISTAGG, STRING_AGG); using a non-aggregate function or no aggregate function will result in an error.

  The length limit for the aliases of aggregate columns in `aggregate_expression` and the columns in the IN clause is 64 characters; exceeding this limit will result in an error.

- **column_expression**

  Must be a simple column name; using the table.column form is not allowed.

- **const_expression**

  Must be a constant expression.

***Example***

```SQL
-- Extract the area number column and count the number of corresponding areas
SELECT * FROM (SELECT branch_name, area_no FROM branches) 
PIVOT(
  COUNT(branch_name)
  FOR area_no 
  IN ('01' AS EastChina, '02' AS WestChina, '03' AS SouthChina, '04' AS NorthChina, '05' AS CentralChina)
);
EastChina                WestChina                SouthChina                NorthChina                CentralChina
--------------------- --------------------- --------------------- --------------------- ---------------------
                    4                     1                     0                     4                     1


-- Show the maximum branch number and the number of cities in East China and West China
SELECT * FROM (SELECT branch_no, branch_name, area_no FROM branches) 
PIVOT(
  COUNT(branch_name) Citys,
  MAX(branch_no) MaxNO 
  FOR area_no 
  IN ('01' AS EastChina, '02' AS WestChina)
);
      EASTCHINA_CITYS EASTCHINA_MAXNO       WESTCHINA_CITYS WESTCHINA_MAXNO
--------------------- --------------- --------------------- ---------------
                    4 0104                                1 0201

```

<span id="unpivotclause" name="unpivotclause" class="yaslink"></span>

###### 1.1.5.6. unpivot\_clause

UNPIVOT is used for row-column transformation, converting columns into rows. This statement is only applicable to HEAP tables.

UNPIVOT can operate on any result set (i.e., a single table query or the result of multiple table joins).

- **INCLUDE | EXCLUDE NULLS**

  This field indicates whether to exclude rows where the converted columns have NULL values; the default is EXCLUDE NULLS.

- **value_column**

  Defines the column name to store the values of the converted columns, which must be a simple column name; using the table.column form is not allowed.

- **pivot_column**

  Defines the column name to store the names of the converted columns, which must also be a simple column name.

- **unpivot_in_clause**

  In this clause, it refers to the column names and aliases of the converted columns, which will serve as the values for the pivot_column.

***Example*** for Heap tables

```SQL
-- Create pivot_table, which stores the total sales price of each product.
CREATE TABLE pivot_table AS
SELECT *
FROM (SELECT product_no, product_name, price FROM product) PIVOT
       (SUM(price) FOR product_name IN ('product001' AS product001, 'product002' AS product002));
SELECT * FROM pivot_table ORDER BY product_no;
PRODUCT_NO  PRODUCT001  PRODUCT002
---------- ----------- -----------
11001               10
11002                           16
10001              100
10002                          200


-- Unpivoting product001 and product002 columns into price and product_name columns, with price column storing the value of the rotated column and product_name storing the column alias of the rotated column. The default excludes NULL values.
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


-- INCLUDE NULLS will include NULL values.
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

```

<span id="joinclause" name="joinclause" class="yaslink"></span>

##### 1.1.6. join\_clause

This statement is used to connect multiple tables, views, AC, and subqueries through join operations. There are several types of join methods:

* Explicitly specifying the JOIN keyword for connecting queries, and distinguishing between INNER JOIN and OUTER JOIN with INNER | OUTER keywords; if unspecified, the default is INNER JOIN.
* Separating each table, view, subquery in the `FROM` clause with commas and specifying conditions in the `WHERE` clause to obtain the results after cross joins; this method is equivalent to INNER JOIN.
* Specifying the `(+)` operator in a condition clause in the `WHERE` clause or in the JOIN ON clause denotes an outer join. 

In ISC Distributed Cluster Deployment, the following rules apply:

-  Multi-table join queries on distributed system views, DBA views, and system tables use local data from each node.
- Distributed views only allow multi-table queries with distributed views, DBA views, USER views, ALL views, system tables, and Standalone Deployment views.
- In distributed queries involving system views, using `ORDER BY`, `GROUP BY`, window functions, aggregate functions, and `LIMIT`, they will only take effect within each node; the final result may not satisfy ordered constraints, etc.

<span id="innercrossjoinclause" name="innercrossjoinclause" class="yaslink"></span>

###### 1.1.6.1. inner\_cross\_join\_clause

Inner join queries are a type of cross-join method. When performing inner join queries on tables A and B, each row of A is compared against every row of B according to the join condition, returning results where both A and B meet the condition. Therefore:

* INNER: The result is the intersection of A and B. The ON condition must be specified afterward.
* CROSS: The result is the Cartesian product of A and B. The ON condition cannot be specified afterward.

***Example*** for ISC Distributed Cluster Deployment

```sql
-- INNER JOIN; the INNER keyword can be omitted
SELECT b.branch_name, a.area_name
FROM branches b
INNER JOIN area a
ON a.area_no = b.area_no
WHERE b.branch_no LIKE '01%';
BRANCH_NAME       AREA_NAME                                                     
----------------- --------------------
Shanghai               EastChina                                                       
Nanjing               EastChina                                                       
Fuzhou               EastChina                                                       
Xiamen               EastChina                                                                  
 
-- Equivalent to the inner join above
SELECT b.branch_name, a.area_name
FROM branches b, area a
WHERE a.area_no=b.area_no AND b.branch_no LIKE '01%';
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
WHERE b.branch_no LIKE '01%';
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

-- Distributed view querying                                                 
SELECT * FROM (SELECT GROUP_ID,GROUP_NODE_ID,NAME,VALUE,DEFAULT_VALUE,IS_DEPRECATED FROM GV$SYSTEM_PARAMETER WHERE GROUP_ID=0);
GROUP_ID GROUP_NODE_ID NAME          VALUE         DEFAULT_VALUE           IS_DEPRECATED
-------- ------------- ------------- ------------- ----------------------- -------------
```

<span id="outerjoinclause" name="outerjoinclause" class="yaslink"></span>

###### 1.1.6.2. outer\_join\_clause

An outer join query specifies a main table, where records in the main table do not require a matching record in the other table and are returned as valid results; in the returned result set, columns for which no matching value was found in the other table are set to NULL. Outer joins must explicitly specify left join or right join.

<span id="outerjointype" name="outerjointype" class="yaslink"></span>

**outer_join_type**

Outer join types include:

- **LEFT \[OUTER\] JOIN**

  Left join query; when A LEFT OUTER JOIN B, A is the main table; every row in A will appear in the result set, with NULL values assigned for values not matched in B.

- **RIGHT \[OUTER\] JOIN**

  Right join query; when A RIGHT OUTER JOIN B, B is the main table; every row in B will appear in the result set, with NULL values assigned for values not matched in A.

- **FULL \[OUTER\] JOIN**

  Full join query; when A FULL OUTER JOIN B, every row in A and B will appear in the result set, with NULL values assigned for values not matched in either A or B.

***Example***

```sql
-- Left join; for Wuhan in branches table, no corresponding area record is matched in area table, thus NULL is set for the area in the result set
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
                                   
-- Right join; for South China in area table, no subordinate city record is matched in branches table, thus NULL is set for the city in the result set
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
                                   
-- Full join; for South China in area table, no subordinate city record is matched in branches table, thus NULL is set for the city in the result set
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

YashanDB supports the use of the `(+)` operator to specify outer joins.

The `(+)` operator can be used in the comparison conditions of `where_clause` and `join_clause`. 

In the comparison condition, `(+)` operates on a particular column, indicating that if the comparison condition cannot be satisfied, the corresponding table should be filled externally, behaving like an outer join. This means the comparison condition will perform an outer join on the table involved in the condition (which might be one or multiple).

The following restrictions apply to the usage of the `(+)` operator:

-  The `(+)` operator can only apply to column fields and cannot be applied to expressions.
- When using the `(+)` operator in a condition in the `where_clause`, it cannot be used together with ANSI-standard Join syntax.
- When using the `(+)` operator in the condition of `join_clause`, the two tables being joined must be ordinary tables and not intermediate results from multiple table joins.
- It is not allowed to use the `(+)` operator to mutually outer join two tables.
- If a condition has the `(+)` operator, only conditions at the same level may be connected using the AND keyword.

***Example***

```sql
-- The `(+)` operator applies to the area table on area_no, meaning that the branches table performs a left outer join on the area table, resulting in NULL values for the area column for unmatched data in the result set.
-- For Wuhan in branches table, no corresponding area record is matched in area table, thus NULL is set for the area in the result set
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

<span id="whereclause" name="whereclause" class="yaslink"></span>

##### 1.1.7. where\_clause

This statement is used to specify the [condition](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/condition) for the query.

<span id="hierarchicalqueryclause" name="hierarchicalqueryclause" class="yaslink"></span>

##### 1.1.8. hierarchical\_query\_clause

This statement is used to specify conditions for hierarchical (parent-child) relationship queries. Use START WITH to get root data and CONNECT BY to specify the parent-child relationships, and group by the [group_by_clause](#groupbyclause) clause to classify and retrieve all data meeting the hierarchical relationship.

This statement is only applicable to HEAP tables.

YashanDB provides the following virtual columns (not defined in table structures but can be queried as column fields) for use in hierarchical relationship queries:

- **LEVEL**

  LEVEL indicates the current level number in the tree structure formed by the hierarchical query. This column's value starts at 1, meaning that data corresponding to the START WITH condition will always have a LEVEL of 1, while the LEVEL of subsequent child nodes will increment accordingly.

- **CONNECT_BY_ISLEAF**

  CONNECT_BY_ISLEAF indicates whether the current data is a leaf node in the tree structure formed by the hierarchical query. If it is a leaf node, the value is 1; if not, it is 0.

- **CONNECT_BY_ISCYCLE**

  CONNECT_BY_ISCYCLE indicates whether the current data would cause a loop in the hierarchical query, meaning whether the current layer contains leaf nodes that are also their parent nodes based on the hierarchical relationships. This column only has significance when the NOCYCLE keyword is simultaneously specified; if the current data would cause a cycle, the result will be 1; otherwise, it will be 0.

YashanDB provides the following identifiers to specify a certain node attribute in the hierarchy:

- **PRIOR**

  The parameter after the PRIOR operator identifies the parent node in the hierarchical query. The parameter cannot be a virtual column, hierarchical query function, operator, pseudo-column, or subquery. PRIOR is typically used in the CONNECT BY clause and cannot be used in the START WITH clause.

- **CONNECT_BY_ROOT**

  The parameter after the CONNECT_BY_ROOT operator identifies the root node in the hierarchical query. The parameter cannot be a virtual column, hierarchical query function, operator, pseudo-column, or subquery. CONNECT_BY_ROOT cannot be used in the CONNECT BY clause and START WITH clause.

YashanDB provides the following specialized functions for hierarchical queries:

- **SYS_CONNECT_BY_PATH**

  `SYS_CONNECT_BY_PATH(col_name, delimiter)`, where `delimiter` indicates the delimiter, can only be a literal value. This function retrieves all node names of `col_name` from the root node to the current node, separated by `delimiter`. SYS_CONNECT_BY_PATH cannot be used in the CONNECT BY clause, START WITH clause, and GROUP BY clause.

<span id="connectbyclause" name="connectbyclause" class="yaslink"></span>

###### 1.1.8.1. connect\_by\_clause

The CONNECT BY clause followed by conditions specifies the conditions for hierarchical relationship queries, where at least one condition must specify the parent-child relationship identified by the PRIOR identifier.

**NOCYCLE**

When the results obtained from the specified condition relationships contain cycles, without specifying NOCYCLE, the system will return an error; specifying NOCYCLE will ignore cycle issues and still return all data.

<span id="startwithclause" name="startwithclause" class="yaslink"></span>

###### 1.1.8.2. start\_with\_clause

The START WITH clause followed by conditions specifies which data meeting the condition should be treated as the root node of the hierarchy, with a LEVEL of 1. The condition may not use rownum.

This statement can be omitted, indicating that all data will be treated as root nodes in the hierarchical query.

When using this statement, it must exist either before or after the `CONNECT BY` clause; it cannot be used independently.

<span id="ordersiblingsbyclause" name="ordersiblingsbyclause" class="yaslink"></span>

###### 1.1.8.3. order\_siblings\_by\_clause

For nodes within the same level under the same parent node, it specifies the ordering sequence and rules through ORDER SIBLINGS BY. This clause's functionality is the same as that of `ORDER BY`.

When using this statement, it must exist before `CONNECT BY` and cannot be used independently.

***Example*** for Heap tables

```sql
-- Create a table area_info that contains hierarchical relationships
CREATE TABLE area_info (id INT, father_id INT, area_name VARCHAR(20));
INSERT INTO area_info VALUES(1,   0, 'Guangdong');
INSERT INTO area_info VALUES(755, 1, 'Shenzhen');
INSERT INTO area_info VALUES(756, 755, 'Longhua');
INSERT INTO area_info VALUES(757, 755, 'Futian');
INSERT INTO area_info VALUES(2,   0, 'Zhejiang');
INSERT INTO area_info VALUES(571, 2, 'Hangzhou');
COMMIT;

-- Display the area hierarchy (specifying the root node)   
SELECT id, father_id, LEVEL,
CONNECT_BY_ROOT area_name AS name, 
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
  
-- Display the area hierarchy (not specifying root nodes, all data will serve as root nodes)   
SELECT id, father_id, LEVEL,
CONNECT_BY_ROOT area_name AS name, 
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

-- Display whether nodes are leaf nodes and if a loop occurs  
SELECT id, father_id, LEVEL, 
CONNECT_BY_ISLEAF AS leaf, CONNECT_BY_ISCYCLE AS iscycle  
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

-- Sort nodes at the same level specified by a specific column  
SELECT  id, father_id, LEVEL,
CONNECT_BY_ROOT area_name AS name, 
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

<span id="groupbyclause" name="groupbyclause" class="yaslink"></span>

##### 1.1.9. group\_by\_clause

This statement is used to aggregate the query result set according to specified conditions.

###### 1.1.9.1. GROUP BY

Columns are defined for grouping after GROUP BY, with multiple columns separated by commas. They must meet the following rules:

- UDTs and built-in UDTs cannot be used as grouping columns.

* Columns or column data appearing in `select_list` must be a subset of the grouping columns or column data. 

    Column is a subset: "SELECT col ,COUNT(*) FROM table GROUP BY col, col2;"

    Column data is a subset: "SELECT LPAD(col), COUNT(*) FROM table GROUP BY col;"

* When functions are involved in grouping columns, the function parameters must match. 

    The statement below is incorrect: "SELECT SUBSTR(col, 1,1), COUNT(*) FROM table GROUP BY SUBSTR(col, 1,2);"

* If DISTINCT or ORDER BY clauses appear simultaneously, the columns in those clauses follow the two rules above.
* Grouping columns cannot contain or nest `*` (asterisk), SEQUENCE, subqueries, and aggregate functions or similar expressions.
* When a numerical grouping column appears, unlike ORDER BY, this statement will not interpret numbers as column positions but rather treat them as literals.

###### 1.1.9.2. HAVING

The HAVING clause constrains the results of a SELECT query with GROUP BY, applying to every grouping in the query results, similar to how the WHERE condition applies to `select_list`. The usage rules are as follows:

* The HAVING clause can be placed before or after the GROUP BY clause.
* The condition after HAVING is a Boolean expression, with syntax identical to that of the WHERE clause's filter_clause. However, it may only include grouping columns, aggregate functions (which may differ from those in the select_list), literals, and subqueries (columns in the subquery need not be grouping columns). In ISC Distributed Cluster Deployment, subqueries cannot be used.
* If there is no GROUP BY, using HAVING directly means this constraint applies to the entire query result. In this case, grouping columns cannot appear in the `select_list` and condition.

***Example*** for Standalone Deployment

```sql
-- Aggregate sales amounts by year; HAVING can specify other aggregate functions and can specify subqueries on other tables
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
 
-- HAVING defines conditions on grouping columns
SELECT 1,year,month,CONCAT(year,month),SUM(amount)
FROM sales_info
GROUP BY year,month
HAVING year>'2015';
           1 YEAR  MONTH CONCAT(YEAR,MONTH)           SUM(AMOUNT)
------------ ----- ----- ------------------ ---------------------
           1 2021  05    202105                               600
           1 2021  10    202110                               300
 
-- Using HAVING independently defines an aggregation constraint for the entire query result, here no grouping columns can appear
SELECT 1,SUM(amount)
FROM sales_info
HAVING MAX(amount)>100;
           1           SUM(AMOUNT)
------------ ---------------------
           1                  2300
```

***Example*** for ISC Distributed Cluster Deployment

```sql
-- Aggregate sales amounts by year; AFTER HAVING can specify the same aggregate function 
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
 
-- HAVING defines conditions on grouping columns
SELECT 1,year,month,CONCAT(year,month),SUM(amount)
FROM sales_info
GROUP BY year,month
HAVING year>'2015';
           1 YEAR  MONTH CONCAT(YEAR,MONTH)           SUM(AMOUNT)
------------ ----- ----- ------------------ ---------------------
           1 2021  05    202105                               600
           1 2021  10    202110                               300
 
-- Using HAVING independently defines an aggregation constraint for the entire query result, here no grouping columns can appear
SELECT 1,SUM(amount)
FROM sales_info
HAVING SUM(amount)>300;
           1           SUM(AMOUNT)
------------ ---------------------
           1                  2300
```

<span id="rollupcubeclause" name="rollupcubeclause" class="yaslink"></span>

###### 1.1.9.3. rollup\_cube\_clause

**ROLLUP**

The ROLLUP keyword is used to specify the ROLLUP operator, which extends the functionality of GROUP BY aggregation. Unlike GROUP BY, which only returns results for each grouping, specifying the ROLLUP keyword will return totals and each group's results.

It is equivalent to creating grouping sets for each hierarchical level of the columns specified after ROLLUP, such as `ROLLUP(A,B,C) == GROUPING SETS((A, B, C), (A, B), (A), ())`.

Constant expressions in projection columns are not treated as grouping keys.

***Example***

```sql
SELECT year,product,salsperson,SUM(quantity) FROM sales_info GROUP BY ROLLUP(year,product,salsperson);

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


SELECT 1, 2, 3 FROM dual GROUP BY ROLLUP (1, 2);

           1            2            3
------------ ------------ ------------
           1            2            3
           1            2            3
           1            2            3

```

**CUBE**

The CUBE keyword is used to specify the CUBE operator, which expands the functions of GROUP BY aggregation. Unlike GROUP BY, which only returns results for each grouping, specifying CUBE will return all combinations of results, including each group's results.

It is equivalent to creating all combinations of grouping sets for the columns specified after CUBE, such as `CUBE(A,B,C) ==  GROUPING SETS((A, B, C), (A, B), (A, C), (B, C), (A), (B), (C), ())`.

Constant expressions in projection columns are not treated as grouping keys.

***Example***

```sql
SELECT year,product,salsperson,SUM(quantity) FROM sales_info GROUP BY CUBE(year,product,salsperson);

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


SELECT 1, 2, 3 FROM dual GROUP BY CUBE (1, 2);

           1            2            3
------------ ------------ ------------
           1            2            3
           1            2            3
           1            2            3
           1            2            3

```

<span id="groupingsetsclause" name="groupingsetsclause" class="yaslink"></span>

###### 1.1.9.4. grouping\_sets\_clause

This statement is used to specify the grouping rules for GROUP BY and aggregate results, equivalent to executing GROUP BY grouping on specified combinations, and then UNION ALL to combine the results.

Constant expressions in projection columns are not treated as grouping keys.

***Example***

```sql
SELECT year,product,salsperson,SUM(quantity) FROM sales_info GROUP BY GROUPING SETS(year,product,salsperson);

YEAR  PRODUCT   SALSPERSON    SUM(QUANTITY) 
----- --------- ------------- ------------- 
                                        120
                0201010011               30
2000                                     20
2001                                     30
2015                                     40
2021                                     60
      11001                             150


SELECT 1, 2, 3 FROM dual GROUP BY GROUPING SETS (1, 2);

           1            2            3
------------ ------------ ------------
           1            2            3
           1            2            3

```

<span id="setoper" name="setoper" class="yaslink"></span>

#### 1.2. set\_oper

Set operations are used to combine two or more query result sets into a single result set, including the following types:

- **UNION**: Merges and filters out duplicate values.
- **UNION ALL**: Merges and retains duplicate values.
- **INTERSECT**: Compares the results of two queries, selecting all the rows that are the same across the result sets, while filtering out duplicates.
- **INTERSECT ALL**: Compares the results of two queries, selecting all the same rows in the result sets while retaining duplicates.
- **MINUS/EXCEPT**: Compares the results of two queries, returning rows in the first result set that are not present in the second result set, while filtering out duplicates.
- **MINUS ALL/EXCEPT ALL**: Compares the results of two queries, returning rows in the first result set that are not present in the second result set while retaining duplicates.

Set operations MINUS and EXCEPT are completely equivalent.

The data types of the result sets involved in set operations must belong to the same classification; otherwise, a function returns a Query column mismatch error:

*   Numeric types: ordered from highest to lowest priority as DOUBLE, FLOAT, NUMBER, BIGINT, INT, SMALLINT, TINYINT, but BIT type can only be in the same operation list with the same type.
*   Character types: ordered from highest to lowest priority as VARCHAR, CHAR.
*   Date and time types: ordered from highest to lowest priority as TIMESTAMP, DATE, TIME, but INTERVAL YEAR TO MONTH and INTERVAL DAY TO SECOND can only be in the same parameter list with the same types.
*   Boolean: BOOLEAN.
*   Other types: RAW.

> **Note**: 
>
> 1. If differing data types share the same classification, the lower priority types are converted to higher priority types.
>
> 2. A result set of constant NULL (resulting from columns in the query being constant NULL) is not subject to the limitations above.
>
> 3. All set operations have a uniform operational priority, with the order of operations as written; double parentheses `()` can be used to adjust desired operational precedence.

***Example***

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

<span id="orderbyclause" name="orderbyclause" class="yaslink"></span>

#### 1.3. order\_by\_clause

This statement is used to sort the query result set based on sort keys.

Sort keys can include:

*   expr: Expression
*   position: Column position in the `select_list`
*   c_alias: Alias defined for the column item in the `select_list`

Sort keys must not be of the following types:

- CLOB
- BLOB
- NCLOB
- CURSOR
- JSON
- UDT
- XMLTYPE
- BFILE

##### 1.3.1. NULLS (FIRST|LAST)

For NULL values, use the NULLS FIRST|NULLS LAST statement to specify whether null values are placed first or last. When this statement is not specified, the default is NULLS LAST for ascending order and NULLS FIRST for descending order.

##### 1.3.2. ASC|DESC

The sorting method may be specified as ascending (ASC) or descending (DESC); if not specified, it defaults to ascending.

***Example***

```sql
SELECT year,branch,SUM(amount) FROM sales_info GROUP BY year,branch ORDER BY SUM(amount)/SUM(quantity);
YEAR  BRANCH           SUM(AMOUNT)
----- ------ ---------------------
2015  0101                     300
2015  0102                     300
2000  0102                     300
2021  0101                     900
2001  0201                     500
 
SELECT year,branch,SUM(amount) FROM sales_info GROUP BY year,branch ORDER BY 2;
YEAR  BRANCH           SUM(AMOUNT)
----- ------ ---------------------
2015  0101                     300
2021  0101                     900
2015  0102                     300
2000  0102                     300
2001  0201                     500
```

<span id="rowlimitingclause" name="rowlimitingclause" class="yaslink"></span>

#### 1.4. row\_limiting\_clause

This statement is used to fetch specified rows from the query result set.

##### 1.4.1. LIMIT

This specifies the number of rows to fetch. LIMIT meets the following rules:

* LIMIT can be an expression, and the result of the expression must be a number.
* When querying HEAP tables, LIMIT may use scalar subqueries and bind parameters.
* If LIMIT is negative, treat it as 0.
* If LIMIT is NULL, return no rows.
* If LIMIT exceeds the number of rows returned by the query, return all rows.
* If LIMIT is a decimal, the decimal part is truncated.

##### 1.4.2. OFFSET

Specifies the starting row offset for fetching, defaulting to an offset of 0 for the first row. OFFSET follows these rules:

*   OFFSET can be an expression, and the result of the expression must be a number.
*   When querying HEAP tables, OFFSET can use scalar subqueries and bind parameters.
*   If OFFSET is negative, it is treated as 0.
*   If OFFSET is NULL, or greater than the number of rows returned by the query, no rows will be returned.
*   If OFFSET is a decimal, the decimal part is truncated.

Executing this statement will have the following impact on the optimizer:

- If both order_by_clause and row_limiting_clause appear in the SQL, the optimizer will rewrite the sorting plan to an ORDER BY STOPKEY plan.
- If group_by_clause, distinct, order_by_clause, and row_limiting_clause appear simultaneously in the SQL, the optimizer may generate a SORT GROUP BY STOPKEY/SORT DISTINCT STOPKEY plan based on cost.

***Example***

```sql
-- Fetch the first four rows from the branches table starting from the first row
SELECT branch_no,branch_name,area_no,address FROM branches LIMIT 4;
BRANCH_NO BRANCH_NAME         AREA_NO ADDRESS         
--------- ------------------- ------- -----------------
0001      Shenzhen                                        
0101      Shanghai              01      Jingan District, Shanghai      
0102      Nanjing              01      City of Nanjing   
0103      Fuzhou              01                        
 
-- Fetch the first four rows from the branches table starting from the fourth row 
SELECT branch_no,branch_name,area_no,address FROM branches LIMIT 4 OFFSET 3;
BRANCH_NO BRANCH_NAME         AREA_NO ADDRESS         
--------- ------------------- ------- -----------------
0103      Fuzhou              01                        
0104      Xiamen              01      Xiamen            
0401      Beijing              04                        
0402      Tianjin              04
```

<span id="offsetfetchclause" name="offsetfetchclause" class="yaslink"></span>

#### 1.5. offset\_fetch\_clause

The functionality of OFFSET FETCH is identical to LIMIT OFFSET, used for fetching specified rows from the result set of the query.

##### 1.5.1. ROWCOUNT

Specifies the number of rows to fetch. ROWCOUNT follows these rules:

*   ROWCOUNT can be an expression, and the result of the expression must be a number.
*   When querying HEAP tables, LIMIT can use scalar subqueries and bind parameters.
*   If ROWCOUNT is negative, it is treated as 0.
*   If ROWCOUNT is NULL, no rows will be returned.
*   If ROWCOUNT is greater than the number of rows returned by the query, all rows will be returned.
*   If ROWCOUNT is a decimal, the decimal part is truncated.

##### 1.5.2. OFFSET

Specifies the starting row offset for fetching, defaulting to an offset of 0 for the first row. OFFSET follows these rules:

*   OFFSET can be an expression, and the result of the expression must be a number.
*   When querying HEAP tables, OFFSET can use scalar subqueries and bind parameters.
*   If OFFSET is negative, it is treated as 0.
*   If OFFSET is NULL, or greater than the number of rows returned by the query, no rows will be returned.
*   If OFFSET is a decimal, the decimal part is truncated.

***Example***

```sql
-- Fetch the first four rows from the branches table starting from the first row
SELECT branch_no,branch_name,area_no,address FROM branches FETCH FIRST 4 ROW ONLY;
BRANCH_NO BRANCH_NAME         AREA_NO ADDRESS         
--------- ------------------- ------- -----------------
0001      Shenzhen                                    
0101      Shanghai              01      Jingan District, Shanghai      
0102      Nanjing              01      City of Nanjing   
0103      Fuzhou              01                        
 
-- Fetch the first four rows from the branches table starting from the fourth row 
SELECT branch_no,branch_name,area_no,address FROM branches OFFSET 3 ROW FETCH FIRST 4 ROW ONLY;
BRANCH_NO BRANCH_NAME         AREA_NO ADDRESS         
--------- ------------------- ------- -----------------
0103      Fuzhou              01                        
0104      Xiamen              01      Xiamen            
0401      Beijing              04                        
0402      Tianjin              04
```

<span id="forupdateclause" name="forupdateclause" class="yaslink"></span>

### 2. for\_update\_clause

This statement is used to lock the rows corresponding to the result set of the query. After locking, other users can only query these rows and cannot perform lock or modification operations until the current transaction ends.

FOR UPDATE can only be specified in the top-level SELECT statement; subqueries and CTEs cannot specify FOR UPDATE.

This statement cannot be combined with DISTINCT, GROUP BY, FLASHBACK, aggregate functions, cursor expressions, etc.

#### 2.1. OF column\_name

Lock the table where the column is located by specifying the specific column field name, without having to lock all the tables that appear after FROM. This is often used when performing updates only on some specific tables (i.e., the table corresponding to the column specified by OF column_name) in a multi-table join query. When omitted, it will lock the records corresponding to all table objects involved in the query.

The column_name must identify an actual column name and cannot be an alias.

It can use table joins, views, synonyms, or aliases defined in the FROM statement to specify the column names.

#### 2.2. WAIT|NOWAIT|SKIP LOCKED

Specifies the operation when at least some of the rows to be locked are currently locked by other users, defaulting to waiting indefinitely until those rows are unlocked before performing the operation and returning.

*   WAIT ntimes: Waits for the specified time, in seconds, up to this time before returning an error.
*   NOWAIT: Does not wait and does not perform the operation; directly returns.
*   SKIP LOCKED: Skips the locked rows and continues executing the other rows. Not applicable to stable data in LSC tables.

This statement is used to define the operation when locking rows; in case of table locks, it directly enters table lock waiting.

***Example*** for Standalone Deployment Heap tables and TAC tables

```sql
SELECT TRIM(address) address FROM branches FOR UPDATE;
 
SELECT * FROM area a, branches b WHERE a.area_no = b.area_no AND a.area_no = '01' FOR UPDATE OF a.DHQ;         -- This will ONLY lock the corresponding ROWS IN the area TABLE
 
SELECT TRIM(address) address FROM branches FOR UPDATE NOWAIT;
 
SELECT TRIM(address) address FROM branches FOR UPDATE WAIT 10;
 
SELECT TRIM(address) address FROM branches FOR UPDATE SKIP LOCKED;
```

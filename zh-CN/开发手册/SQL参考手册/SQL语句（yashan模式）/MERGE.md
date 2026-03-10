通用描述
----

MERGE语句整合了多步的SELECT/UPDATE/INSERT/DELETE操作，使用一次该语句即可实现根据条件（Condition）把源（Source）对象记录整合到目标（Target）对象的功能。

本语句适用于单机HEAP/TAC表和共享集群部署。

语句中的merge_update_clause和merge_insert_clause至少要定义一个。

MERGE语句是对SELECT/UPDATE/INSERT/DELETE的操作整合，也受到在其语句中出现的SELECT/UPDATE/INSERT/DELETE语句相同的条件约束。

MERGE是确定性语句，不能在同一MERGE语句中多次更新目标表的同一行。

语句定义
----

**merge::=**

```ebnf+diagram
syntax::= MERGE [hint] INTO target_table_clause USING source_table_clause ON "(" condition ")" (merge_update_clause | merge_insert_clause) {" " (merge_update_clause | merge_insert_clause)}
```

**[target\_table\_clause](#targettableclause)::=**

```ebnf+diagram
syntax::= ([schema "."] (table_name [partition_extension_clause] | view_name) | ( "(" (subquery) [subquery_restriction_clause] ")")) [t_alias]
```

**subquery查看[SELECT](./SELECT)中描述**

**[partition\_extension\_clause](#partitionextensionclause)::=**

```ebnf+diagram
syntax::= partition (("(" partition ")")|(for "(" (partition_key_value) {"," (partition_key_value)} ")"))
```

**[subquery\_restriction\_clause](#subqueryrestrictionclause)::=**

```ebnf+diagram
syntax::= ((WITH) ((READ ONLY) | (CHECK OPTIION)) [CONSTRAINT constraint])
```

**[source\_table\_clause](#sourcetableclause)::=**

```ebnf+diagram
syntax::= ([schema "." ] ((table [partition_extension_clause])|view)|"(" subquery ")") [t_alias]
```

**subquery查看[SELECT](./SELECT)中描述**

**[merge\_update\_clause](#mergeupdateclause)::=**

```ebnf+diagram
syntax::= WHEN MATCHED THEN UPDATE SET (column "=" (expr|DEFAULT)) {"," (column "=" (expr|DEFAULT))} [where_clause] [DELETE where_clause]
```

**[merge\_insert\_clause](#mergeinsertclause)::=**

```ebnf+diagram
syntax::= WHEN NOT MATCHED THEN INSERT ["(" (column) {"," (column)}")"] VALUES "(" (expr|DEFAULT) {"," (expr|DEFAULT)} ")" [where_clause]
```

### 1. hint

该语句用于提出给定的方案到优化器（Optimizer ），使其按照此方案生成语句的执行计划。[查看hint说明](../通用SQL语法/hint)。

<span id="targettableclause" name="targettableclause" class="yaslink"></span>

### 2. target\_table\_clause

该语句用于指定MERGE操作的目标，可以为本地表名称、表分区名称、视图名称或子查询，可对其指定一个别名。

基于视图或子查询对其基表操作时，约束如下：

- 子查询或者目标视图的子查询不能是多基表join。
- 不能使用分组、聚合、去重、ROWNUM、CONNECT BY、sample、集合操作、窗口函数、for update、表函数等算子或操作符对基表操作。
- 操作的列必须对应基表的列（不能是其他表达式类型）。

#### 2.1. table\_name

目标表的名称。

#### 2.2. view\_name

目标视图的名称。

<span id="partitionextensionclause" name="partitionextensionclause" class="yaslink"></span>

#### 2.3. partition\_extension\_clause

与[INSERT](./INSERT)语句中的描述一致。

<span id="subqueryrestrictionclause" name="subqueryrestrictionclause" class="yaslink"></span>

#### 2.4. subquery\_restriction\_clause

与[CREATE VIEW](./CREATE VIEW)语句中subquery_restriction_clause的描述一致。

可缺省。

#### 2.5. t\_alias

定义别名。

<span id="sourcetableclause" name="sourcetableclause" class="yaslink"></span>

### 3. source\_table\_clause

该语句用于指定MERGE操作的源，可以为表|表分区、视图或子查询的结果集。

#### 3.1. view

视图的名称。

#### 3.2. subquery

与[SELECT](./SELECT)语句中的描述一致。

<span id="condition" name="condition" class="yaslink"></span>

### 4. condition

该语句为YashanDB通用[条件语句](../通用SQL语法/condition)，用于指定MERGE选择下一步操作的条件，对目标表里的每行记录执行此条件判断，为真（MATCHED）时，将执行merge_update_clause；为假（NOT MATCHED）时，将执行merge_insert_clause。

condition可以为任何能产生Boolean结果的表达式，例如：

*   TRUE，Boolean结果为TRUE。
*   1=2，Boolean结果为FALSE。
*   target.column=source.column，当Target某行按column字段在Source中能匹配到行记录时，Boolean结果为TRUE，否则为FALSE。

<span id="mergeupdateclause" name="mergeupdateclause" class="yaslink"></span>

#### 4.1. merge\_update\_clause

该语句用于在[condition](#condition)结果为真时，对目标表指定的列字段执行UPDATE语句，指定多列用`,`分隔。

存在UPDATE子句时，会让MERGE语句中触发的INSERT、DELETE触发器中`UPDATING ('*column*')`条件谓词的结果在更新的列匹配时返回TRUE。

对列字段赋值的数据可以为：

*   [expr](../通用SQL语法/expr)表达式
*   DEFAULT：与[UPDATE](./UPDATE)语句中的DEFAULT使用方法一致。

#### 4.2. where\_clause

对于目标表中根据上面ON condition进入到UPDATE语句的行记录，执行一次条件判断（语法同[condition](#condition)），满足条件的行记录才执行数据的更新。

#### 4.3. DELETE where\_clause

对于目标表中已被执行UPDATE语句的行记录，执行一次条件判断（语法同[condition](#condition)），满足条件的记录将从目标表中删除。

DELETE时会直接执行DELETE操作而不是先执行UPDATE再DELETE，该行为会影响UPDATE/DELETE事件触发器的表现。

示例（单机HEAP/TAC表、共享集群部署）

```sql
-- employees为一张员工信息表，包含如下五条数据
SELECT branch,department,employee_no,employee_name,sex,entry_date FROM employees;
BRANCH DEPARTMENT EMPLOYEE_NO   EMPLOYEE_NAME SEX   ENTRY_DATE                       
------ ---------- ------------- ------------- ----- -------------------------------- 
0101   000        0101000001    Mask          1     2020-09-08                   
0101   000        0101000002    John          1     2017-12-13                  
0201   010        0201010011    Anna          0     2022-08-09                  
0201   008        0201008003    Jack          1     2021-07-05                  
0101   008        0201008004    Jim           1     2022-11-17 
  
-- 创建与employees同构的employees_merge表
CREATE TABLE employees_merge AS SELECT * FROM employees WHERE 1=2;
INSERT INTO employees_merge VALUES ('0101','008','0201008003','Jim','0',DATE '2021-11-17');
INSERT INTO employees_merge VALUES ('0101','000','0101000002','John','1',DATE '2021-11-17');
COMMIT;
  
-- 将employees_merge表中的数据merge
  
-- 由于0201008003号员工在employees中存在两行记录，需利用此处的department与branch组合条件过滤成一条，否则更新失败
MERGE INTO employees_merge b
USING (SELECT * FROM employees) a
ON (a.employee_no=b.employee_no)
WHEN MATCHED THEN UPDATE SET b.sex=a.sex,b.entry_date=a.entry_date
WHERE a.department='008' AND a.branch='0201';
  
-- employees_merge表merge后数据被更新如下
SELECT branch,department,employee_no,employee_name,sex,entry_date FROM employees_merge;
BRANCH DEPARTMENT EMPLOYEE_NO   EMPLOYEE_NAME SEX   ENTRY_DATE                       
------ ---------- ------------- ------------- ----- -------------------------------- 
0101   008        0201008003    Jim           1     2021-07-05                  
0101   000        0101000002    John          1     2021-11-17
  
-- 添加DELETE语句后重新merge，employees_merge表的Jim员工数据先被更新然后被删除
MERGE INTO employees_merge b
USING (SELECT * FROM employees) a
ON (a.employee_no=b.employee_no)
WHEN MATCHED THEN UPDATE SET b.sex=a.sex,b.entry_date=a.entry_date
WHERE a.department='008' AND a.branch='0201'
DELETE WHERE b.department='008';
  
SELECT branch,department,employee_no,employee_name,sex,entry_date FROM employees_merge;
BRANCH DEPARTMENT EMPLOYEE_NO   EMPLOYEE_NAME SEX   ENTRY_DATE                       
------ ---------- ------------- ------------- ----- -------------------------------- 
0101   000        0101000002    John          1     2021-11-17
```

<span id="mergeinsertclause" name="mergeinsertclause" class="yaslink"></span>

### 5. merge\_insert\_clause

该语句用于在[condition](#condition)结果为假时，对目标表执行INSERT语句。不指定列字段表示按目标表定义的列字段顺序逐个匹配，指定多个列字段用`,`分隔。

对列字段赋值的数据可以为：

*   [expr](../通用SQL语法/expr)表达式
*   DEFAULT：与[INSERT](./INSERT)语句中的DEFAULT使用方法一致。

#### 5.1. where\_clause

对于目标表中根据上面ON condition进入到INSERT语句的行记录，执行一次条件判断（语法同[condition](#condition)），满足条件的行记录才插入到目标表中。

示例（单机HEAP/TAC表、共享集群部署）

```sql
-- employees为一张员工信息表，包含如下五条数据
SELECT branch,department,employee_no,employee_name,sex,entry_date FROM employees;
BRANCH DEPARTMENT EMPLOYEE_NO   EMPLOYEE_NAME SEX   ENTRY_DATE                       
------ ---------- ------------- ------------- ----- -------------------------------- 
0101   000        0101000001    Mask          1     2020-09-08                   
0101   000        0101000002    John          1     2017-12-13                   
0201   010        0201010011    Anna          0     2022-08-09                   
0201   008        0201008003    Jack          1     2021-07-05                  
0101   008        0201008004    Jim           1     2022-11-17
  
-- 创建与employees同构的employees3表
CREATE TABLE employees3 AS SELECT * FROM employees WHERE 1=2;
INSERT INTO employees3 VALUES ('0101','000','0101000002','John','1',DATE '2021-11-17');
COMMIT;
  
-- 将empoyees1表中的数据merge
MERGE INTO employees3 b
USING (SELECT * FROM employees) a
ON (a.employee_no=b.employee_no)
WHEN MATCHED THEN UPDATE SET b.sex=a.sex,b.entry_date=a.entry_date
WHEN NOT MATCHED THEN INSERT VALUES (a.branch,a.department,a.employee_no,a.employee_name,a.sex,a.entry_date);
  
-- employees3中的John员工记录被更新，且在employees中没有的记录被插入
SELECT branch,department,employee_no,employee_name,sex,entry_date FROM employees3;
BRANCH DEPARTMENT EMPLOYEE_NO   EMPLOYEE_NAME SEX   ENTRY_DATE                       
------ ---------- ------------- ------------- ----- -------------------------------- 
0101   000        0101000002    John          1     2017-12-13                 
0101   000        0101000001    Mask          1     2020-09-08                  
0201   010        0201010011    Anna          0     2022-08-09                   
0201   008        0201008003    Jack          1     2021-07-05                   
0101   008        0201008004    Jim           1     2022-11-17   
```

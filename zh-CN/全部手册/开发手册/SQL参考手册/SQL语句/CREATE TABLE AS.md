## 通用描述

CREATE TABLE AS语句根据一个子查询结果创建一张新的表对象，包括列字段及其类型，同时插入查询结果包含的数据集。

创建的表的列字段与子查询的列项具有相同的名称和类型，但列字段名称可以通过指定语句重置，类型则不可改变。同时还可以指定表的如下属性：

*   列约束
*   存储参数
*   分区（不能为临时表创建分区）
*   表空间

语句定义
----

**create table as::=**

```ebnf+diagram
syntax::= CREATE [(GLOBAL|PRIVATE) TEMPORARY] TABLE [schema "."] table_name [column_clause] [table_properties] AS subquery
```

**column_clause::=**

```ebnf+diagram
syntax::= "(" (out_of_line_constraint|column_name [DEFAULT default_expr] [inline_constraint])      {"," (out_of_line_constraint|column_name [DEFAULT default_expr] [inline_constraint])} ")"
```

### 1. GLOBAL|PRIVATE TEMPORARY

该语句用于指定创建的表为临时表，语法同[CREATE TABLE](./CREATE TABLE)中描述。

### 2. table\_name

该语句用于指定创建的表的名称，语法同[CREATE TABLE](./CREATE TABLE)中描述。

### 3. out\_of\_line\_constraint

该语句用于定义表的行外约束项，语法同[CREATE TABLE](./CREATE TABLE)中描述。

### 4. column\_name

该语句用于重置表的列字段名称，不定义此项时，默认按子查询结果的所有列项及类型创建表的各项列字段及类型；定义此项时，则必须与子查询结果的列项一一对应，且名称符合YashanDB的[对象命名规范](../基本SQL元素/标识符)。

### 5. DEFAULT default\_expr

定义列字段的缺省值，可省略，default_expr可以为字面量|运算式|函数等表达式。

### 6. inline\_constraint

为列字段定义行内约束项，语法同[CREATE TABLE](./CREATE TABLE)中描述。

### 7. table\_properties

该语句用于指定表的存储、分区、表空间等属性，语法同[CREATE TABLE](./CREATE TABLE)中描述。

### 8. subquery

存算一体分布式集群部署中，不允许子查询语句包含分布式系统视图，否则返回错误。

语法同[SELECT](./SELECT)中描述。

示例

```sql
--1、复制表的所有列及数据到新表，包括列字段名称、列字段类型和表数据，不包括约束项、存储参数、分区、表空间
CREATE TABLE branches_copy_all AS SELECT * FROM branches;
--添加false条件可以只复制表结构而不复制数据
CREATE TABLE branches_copy_no_data AS SELECT * FROM branches WHERE 1=2;
  
--2、复制表的部分列，重置列字段名称，并定义行内约束，此时列字段必须与子查询列项一一对应
CREATE TABLE branches_copy_column(branch_no1 PRIMARY KEY,branch_name) AS SELECT branch_no,branch_name bname FROM branches;
  
--3、复制表，并定义行外约束和表空间
CREATE TABLE branches_copy_add(FOREIGN KEY (area) REFERENCES area(area_no)) TABLESPACE yashan
AS SELECT branch_no,branch_name,area_no area FROM branches;
```

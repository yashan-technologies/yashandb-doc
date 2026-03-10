### 通用描述

ALTER TABLE语句用于修改表的的结构、定义以及相关属性。

### 语句定义

**alter table::=**

```ebnf+diagram
syntax::= ALTER TABLE [schema "."] table_name 
(alter_column_clause|alter_index_clause|alter_constraint_clause|AUTO_INCREMENT ["="] value)
```

**[alter\_column\_clause](#altercolumnclause)::=**

```ebnf+diagram
syntax::= change_column_clause|modify_column_clause|add_column_clause
```

**[change_column_clause](#changecolumnclause)::=**

```ebnf+diagram
syntax::= CHANGE (column_name new_column_name (dataType|DEFAULT default_expr|inline_constraint) 
{" " (dataType|DEFAULT default_expr|inline_constraint)}) 
{"," (column_name (dataType|DEFAULT default_expr|inline_constraint) 
{" " (dataType|DEFAULT default_expr|inline_constraint)})}
```
行内约束含义详见[inline_constraint](../通用SQL语法/constraint（mysql模式）)。

**[modify_column_clause](#modifycolumnclause)::=**

```ebnf+diagram
syntax::= MODIFY (column_name (dataType|DEFAULT default_expr|inline_constraint) 
{" " (dataType|DEFAULT default_expr|inline_constraint)}) 
{"," (column_name (dataType|DEFAULT default_expr|inline_constraint) 
{" " (dataType|DEFAULT default_expr|inline_constraint)})}
```
行内约束含义详见[inline_constraint](../通用SQL语法/constraint（mysql模式）)。

**[add\_column\_clause](#addcolumnclause)::=**

```ebnf+diagram
syntax::= ADD [COLUMN] "(" (column_definition) {"," (column_definition)} ")"
```
column_definition的含义及使用介绍同CREATE TABLE中的[column_definition](./CREATE TABLE.html#columndefinition)。

**[alter\_index\_clause](#alterindexclause)::=**

```ebnf+diagram
syntax::= drop_index_clause|add_index_clause
```

**[add\_index\_clause](#addindexclause)::=**

```ebnf+diagram
syntax::= ADD ((UNIQUE [KEY])|(PRIMARY KEY)|INDEX|KEY) index_name"(" index_expr [DESC|ASC]{ "," index_expr [DESC|ASC]} ")" [index_type]
```
index_expr，index_type的含义及使用介绍同CREATE INDEX中的[index_expr](./CREATE INDEX.html#indexexpr)，[index_type](./CREATE INDEX.html#indextype)。

**[drop\_index\_clause](#dropindexclause)::=**

```ebnf+diagram
syntax::= DROP INDEX index_name
```

**[alter\_constraint\_clause](#alterconstraintclause)::=**

```ebnf+diagram
syntax::= drop_constraint_clause|add_constraint_clause
```

**[add\_constraint\_clause](#addconstraintclause)::=**

```ebnf+diagram
syntax::= ADD CONSTRAINT  constraint_name ((UNIQUE "(" ((column_name) {"," (column_name)}) ")")|(PRIMARY KEY "(" ((column_name) {"," (column_name)}) ")")|(FOREIGN KEY "(" ((column_name) {"," (column_name)}) ")" references_clause)|CHECK condition) 
```

**[drop\_constraint\_clause](#dropconstraintclause)::=**

```ebnf+diagram
syntax::= DROP ((PRIMARY KEY)|(FOREIGN KEY forgien_constraint_name)|(CONSTRAINT constraint_name)) 
```

#### 1. AUTO\_INCREMENT = value

该语句用于修改表的自增值。

<span id="altercolumnclause" name="altercolumnclause" class="yaslink"></span>

#### 2. alter\_column\_clause

该语句用于指定对表的列字段的操作。

<span id="changecolumnclause" name="changecolumnclause" class="yaslink"></span>

#### 3. change\_column\_clause

该语句用于对列字段的数据类型、默认值、约束等属性进行修改，同时修改多个列字段时需在`()`中以`,`分隔。

<span id="modifycolumnclause" name="modifycolumnclause" class="yaslink"></span>

#### 4. modify\_column\_clause

该语句用于对列字段的数据类型、默认值、约束等属性进行修改，同时修改多个列字段时需在`()`中以`,`分隔。

<span id="addcolumnclause" name="addcolumnclause" class="yaslink"></span>

#### 5. add\_column\_clause

该语句用于为表增加列字段，同时增加多项时在`()`中以`,`分隔。COLUMN关键字可省略。

<span id="alterindexclause" name="alterindexclause" class="yaslink"></span>

#### 6. alter\_index\_clause

该语句用于为表增加或删除索引。

<span id="addindexclause" name="addindexclause" class="yaslink"></span>

#### 7. add\_index\_clause

该语句用于为表增加索引。多个索引列在`()`中以`,`分隔。可以指定索引选项USING BTREE。

<span id="dropindexclause" name="dropindexclause" class="yaslink"></span>

#### 8. drop\_index\_clause

该语句用于为表删除索引。

<span id="alterconstraintclause" name="alterconstraintclause" class="yaslink"></span>

#### 9. alter\_constraint\_clause

该语句用于为表增加或删除约束。

<span id="addconstraintclause" name="addconstraintclause" class="yaslink"></span>

#### 10. add\_constraint\_clause

该语句用于为表增加约束。

<span id="dropconstraintclause" name="dropconstraintclause" class="yaslink"></span>

#### 11. drop\_constraint\_clause

该语句用于为表删除约束。

示例（单机HEAP表）

```sql
CREATE TABLE orders_info (
    id INT,
    num INT,
    total INT
);
ALTER TABLE orders_info MODIFY total DOUBLE;
ALTER TABLE orders_info CHANGE num num DEFAULT 10;
ALTER TABLE orders_info ADD UNIQUE INDEX order_uk(id);
ALTER TABLE orders_info DROP INDEX ORDER_UK;
ALTER TABLE orders_info ADD CONSTRAINT order_pk PRIMARY key(id);
ALTER TABLE orders_info DROP PRIMARY key;
```

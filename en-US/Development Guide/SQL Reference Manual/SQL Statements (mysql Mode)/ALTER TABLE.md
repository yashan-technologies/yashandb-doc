### General Description

The ALTER TABLE statement is used to modify the structure, definition, and related properties of a table.

### Statement Definition

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
The meaning of inline constraints can be found in [inline_constraint](../General SQL Syntax/constraint (mysql Mode)).

**[modify_column_clause](#modifycolumnclause)::=**

```ebnf+diagram
syntax::= MODIFY (column_name (dataType|DEFAULT default_expr|inline_constraint) 
{" " (dataType|DEFAULT default_expr|inline_constraint)}) 
{"," (column_name (dataType|DEFAULT default_expr|inline_constraint) 
{" " (dataType|DEFAULT default_expr|inline_constraint)})}
```
The meaning of inline constraints can be found in [inline_constraint](../General SQL Syntax/constraint (mysql Mode)).

**[add\_column\_clause](#addcolumnclause)::=**

```ebnf+diagram
syntax::= ADD [COLUMN] "(" (column_definition) {"," (column_definition)} ")"
```
The meaning and usage of column_definition are introduced in [column_definition](CREATE TABLE.html#columndefinition) in CREATE TABLE.

**[alter\_index\_clause](#alterindexclause)::=**

```ebnf+diagram
syntax::= drop_index_clause|add_index_clause
```

**[add\_index\_clause](#addindexclause)::=**

```ebnf+diagram
syntax::= ADD ((UNIQUE [KEY])|(PRIMARY KEY)|INDEX|KEY) index_name"(" index_expr [DESC|ASC]{ "," index_expr [DESC|ASC]} ")" [index_type]
```
The meanings and usage of index_expr and index_type are introduced in [index_expr](CREATE INDEX.html#indexexpr) and [index_type](CREATE INDEX.html#indextype) in CREATE INDEX.

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

This statement is used to modify the auto-increment value of a table.

<span id="altercolumnclause" name="altercolumnclause" class="yaslink"></span>

#### 2. alter\_column\_clause

This statement is used to specify operations on columns of the table.

<span id="changecolumnclause" name="changecolumnclause" class="yaslink"></span>

#### 3. change\_column\_clause

This statement is used to modify attributes such as data type, default value, and constraints of a column. When modifying multiple columns, they should be separated by `,` within `()`.

<span id="modifycolumnclause" name="modifycolumnclause" class="yaslink"></span>

#### 4. modify\_column\_clause

This statement is used to modify attributes such as data type, default value, and constraints of a column. When modifying multiple columns, they should be separated by `,` within `()`.

<span id="addcolumnclause" name="addcolumnclause" class="yaslink"></span>

#### 5. add\_column\_clause

This statement is used to add columns to the table. When adding multiple columns, they should be separated by `,` within `()`. The COLUMN keyword is optional.

<span id="alterindexclause" name="alterindexclause" class="yaslink"></span>

#### 6. alter\_index\_clause

This statement is used to add or drop indexes for the table.

<span id="addindexclause" name="addindexclause" class="yaslink"></span>

#### 7. add\_index\_clause

This statement is used to add indexes to the table. Multiple index columns are separated by `,` within `()`. The index option USING BTREE can be specified.

<span id="dropindexclause" name="dropindexclause" class="yaslink"></span>

#### 8. drop\_index\_clause

This statement is used to drop indexes from the table.

<span id="alterconstraintclause" name="alterconstraintclause" class="yaslink"></span>

#### 9. alter\_constraint\_clause

This statement is used to add or drop constraints for the table.

<span id="addconstraintclause" name="addconstraintclause" class="yaslink"></span>

#### 10. add\_constraint\_clause

This statement is used to add constraints to the table.

<span id="dropconstraintclause" name="dropconstraintclause" class="yaslink"></span>

#### 11. drop\_constraint\_clause

This statement is used to drop constraints from the table.

***Example*** for Standalone Deployment Heap tables

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

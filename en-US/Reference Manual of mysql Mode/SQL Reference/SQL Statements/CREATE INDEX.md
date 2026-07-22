## General Description

CREATE INDEX is used to create an index object on a specified table.

Indexes can have the same name between different tables of the same user, but index names on the same table must be unique.

UNIQUE INDEX cannot be set to DESC (descending).

## Statement Definition

**create index::=**

```ebnf
= CREATE [UNIQUE] INDEX index_name [index_type] ON [schema "."] table_name "(" index_expr [DESC|ASC]{ "," index_expr [DESC|ASC]} ")" [index_option] [index_type].
```
**[index_expr](#indexexpr)::=**

```ebnf
= column_name|column_expression.
```

**[index_option](#indexoption)::=**

```ebnf
= KEY_BLOCK_SIZE ["="] value | ALGORITHM ["="] {DEFAULT | INPLACE | COPY} | LOCK ["="] {DEFAULT | NONE | SHARED | EXCLUSIVE} | COMMENT 'string' | WITH PARSER parser_name.
```
**[index_type](#indextype)::=**

```ebnf
= USING (BTREE | HASH).
```

<span id="indexexpr" name="indexexpr"></span>

### UNIQUE

This statement is used to specify that the created index is a unique index, meaning that the values in the indexed column fields must be unique. 

In mysql mode, unique index columns containing NULL values are not subject to uniqueness checks. Therefore, in a composite unique index, if some of the indexed columns contain NULL values—such as two rows with `(1, NULL)`, no unique constraint violation occurs, and the unique index can be created successfully.

### index_expr

Specifies the columns or expressions on which the index is based; multiple items are separated by `,`.

In mysql mode, only BTree indexes can be created based on columns. When a HASH index is created based on columns or a function-based index is created, the parsing and execution will be performed in accordance with yashan mode, and the database will provide services normally. For detailed syntax requirements, please refer to [CREATE INDEX based on yashan mode](../../../Development Guide/SQL Reference Manual/SQL Statements/CREATE INDEX.md#indexexpr).

<span id="indexoption" name="indexoption"></span>

### index_option

Specifies index-related attribute options, including KEY_BLOCK_SIZE, ALGORITHM, LOCK, etc., which are used for compatibility and have no practical meaning.

<span id="indextype" name="indextype"></span>

### index_type

Specifies the storage type of the index; syntactically supports BTREE and HASH, but in practical use, only BTREE is supported.

***Example*** for Standalone Deployment Heap tables

```sql
CREATE UNIQUE INDEX idx_orders_info_1 ON orders_info (id, area);
-- or with USING BTREE
CREATE INDEX idx_orders_info_1 USING BTREE ON orders_info (id, area);
-- or placing USING BTREE afterwards
CREATE INDEX idx_orders_info_1 ON orders_info (id, area) USING BTREE;
```

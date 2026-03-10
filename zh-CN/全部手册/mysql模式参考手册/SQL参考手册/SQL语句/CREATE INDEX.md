## 通用描述

CREATE INDEX用于在指定的表上创建一个索引对象。

同一用户的不同表之间索引可以重名，但同一个表的不同索引名称必须唯一。

UNIQUE INDEX不允许设置为DESC（降序）。

## 语句定义

**create index::=**

```ebnf+diagram
syntax::= CREATE [UNIQUE] INDEX index_name [index_type] ON [schema "."] table_name "(" index_expr [DESC|ASC]{ "," index_expr [DESC|ASC]} ")" [index_option] [index_type]
```
**[index_expr](#indexexpr)::=**

```ebnf+diagram
syntax::= column_name|column_expression
```

**[index_option](#indexoption)::=**

```ebnf+diagram
syntax::= KEY_BLOCK_SIZE ["="] value | ALGORITHM ["="] {DEFAULT | INPLACE | COPY} | LOCK ["="] {DEFAULT | NONE | SHARED | EXCLUSIVE} | COMMENT "string" | WITH PARSER parser_name
```
**[index_type](#indextype)::=**

```ebnf+diagram
syntax::= USING (BTREE | HASH)
```

<span id="indexexpr" name="indexexpr" class="yaslink"></span>

### index\_expr

指定索引所基于的列或列表达式，多项组合指定时以`,`分隔。

mysql模式仅支持基于列创建BTree索引，当基于列创建HASH索引或创建函数索引时，将按照yashan模式进行解析执行，数据库正常提供服务。详细的语法要求可参考[基于yashan模式CREATE INDEX](../../../开发手册/SQL参考手册/SQL语句/CREATE INDEX.html#indexexpr)。

<span id="indexoption" name="indexoption" class="yaslink"></span>

### index\_option

指定索引相关属性选项，包括KEY_BLOCK_SIZE、ALGORITHM、LOCK等，仅用于兼容，无实际含义。

<span id="indextype" name="indextype" class="yaslink"></span>

### index\_type

指定索引存储类型，语法上支持BTREE和HASH，实际使用过程中仅支持BTREE。

示例（HEAP表）

```sql
CREATE UNIQUE INDEX idx_orders_info_1 ON orders_info (id, area);
--或者带USING BTREE
CREATE INDEX idx_orders_info_1 USING BTREE ON orders_info (id, area);
--或者USING BTREE放在后面
CREATE INDEX idx_orders_info_1 ON orders_info (id, area) USING BTREE;
```

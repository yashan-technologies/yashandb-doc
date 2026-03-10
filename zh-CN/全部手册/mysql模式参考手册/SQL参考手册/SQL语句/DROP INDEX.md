## 通用描述

DROP INDEX用于删除一个索引对象，此时该索引所占的数据空间也全部被释放。

## 语句定义

**drop index::=**

```ebnf+diagram
syntax::= DROP INDEX index_name ON [schema "."] table_name
```
**alter table drop index::=**

```ebnf+diagram
syntax::= ALTER TABLE [schema "."] table_name DROP INDEX index_name
```
示例（HEAP表）

```sql
CREATE TABLE sales_info (
    id INT NOT NULL,
    fname VARCHAR(30),
    lname VARCHAR(30),
    hired DATE NOT NULL DEFAULT '1970-01-01',
    separated DATE NOT NULL DEFAULT '9999-12-31',
    job_code INT,
    store_id INT
);
CREATE INDEX idx_sales_info_1 USING BTREE ON sales_info(id);
DROP INDEX idx_sales_info_1 ON sales_info;
-- 或者
ALTER TABLE sales_info DROP INDEX idx_sales_info_1;
```

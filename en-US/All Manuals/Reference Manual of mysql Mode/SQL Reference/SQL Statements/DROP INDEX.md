## General Description

DROP INDEX is used to delete an index object, and at this time, all the data space occupied by the index is also released.

## Statement Definition

**drop index::=**

```ebnf+diagram
syntax::= DROP INDEX index_name ON [schema "."] table_name
```
**alter table drop index::=**

```ebnf+diagram
syntax::= ALTER TABLE [schema "."] table_name DROP INDEX index_name
```
***Example*** for Standalone Deployment Heap tables

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
-- or
ALTER TABLE sales_info DROP INDEX idx_sales_info_1;
```

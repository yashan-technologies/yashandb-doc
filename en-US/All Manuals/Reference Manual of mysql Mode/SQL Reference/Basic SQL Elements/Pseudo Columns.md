The behavior of pseudocolumns is similar to that of ordinary columns, but their data is not stored in the table. Users can SELECT pseudocolumns but cannot perform INSERT, UPDATE, or DELETE operations on them.

YashanDB (mysql mode) supports the ROWID pseudocolumn, which is implemented through the variable __ysmy_rowid to represent the physical address of a row.

- This pseudocolumn is applicable only to HEAP tables.

- This pseudocolumn cannot be used in the WHEN clauses, insert_values_clause, or subquery for [multi-table conditional inserts](../../../开发手册/SQL参考手册/SQL语句/INSERT.html#conditionalinsertclause).

- This pseudocolumn cannot be used in the SELECT clause of `create view as select` and `create table as select` SQL statements.

- ROWID is supported as a variable name.

***Example*** for Heap tables

```sql
SELECT __ysmy_rowid FROM area;

SET @ROWID=0; 
SELECT @ROWID:=@ROWID+1 AS ROWID,area.* FROM area;
```

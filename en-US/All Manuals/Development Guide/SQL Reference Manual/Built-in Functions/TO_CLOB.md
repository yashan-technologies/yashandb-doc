```ebnf+diagram
to_clob::= TO_CLOB "(" expr ["," csid] ["," mime_type] ")"
```

The TO_CLOB function converts the character set of the [expr](../General SQL Syntax/expr) data source to the database character set and returns it as a CLOB value.

The TO_CLOB function only supports usage on HEAP tables.

**expr**

A general expression that requires conversion.

- When the value of expr is NULL, the function returns NULL.

- When expr is of BLOB/BFILE type, the function supports 1-3 parameters. When expr is not a BLOB/BFILE type, the function only supports 1 parameter.

**csid**

Specifies the character set ID of the BLOB/BFILE data. If the character set of the BLOB/BFILE data matches the database's current character set, the value of csid can be specified as 0 or csid can be completely omitted.


The configurable range for csid is as follows:

|Character Set |csid Value |
|:---------|:------|
| ASCII    | 1     |
| GBK      | 852   |
| UTF-8    | 873   |
| ISO88591 | 31    |
| UTF-16   | 2000  |
| GB18030  | 854   |


**mime_type**

This parameter is only for syntax compatibility and has no practical significance.

***Example***

```sql
DROP TABLE IF EXISTS to_clob;
CREATE TABLE TO_CLOB(id INT, blob_col BLOB);
INSERT INTO to_clob VALUES(2, '2D4E610062006300640087656500660067006800575B69006A006B00267B');

SELECT TO_CLOB(blob_col, 2000) FROM to_clob WHERE id = 2;

TO_CLOB(BLOB_COL,2000)
----------------------------------------------------------------
Chinese abcd text efgh characters ijk
```

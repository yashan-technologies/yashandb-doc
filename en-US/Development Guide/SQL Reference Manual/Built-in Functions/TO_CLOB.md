```ebnf
to_clob = TO_CLOB "(" expr ["," csid] ["," mime_type] ")".
```

The TO_CLOB function converts the character set of the [expr](../General SQL Syntax/expr) data source to the database character set and returns it as a CLOB value.

This function only supports usage on HEAP tables.

**expr**

A general expression that requires conversion.

When the value of expr is NULL, the function returns NULL.

**csid**

Specifies the character set ID of the BLOB/BFILE data. If the character set of the BLOB/BFILE data matches the database's current character set, the value of csid can be specified as 0 or csid can be completely omitted.

`csid` can only be specified when expr is of BLOB/BFILE type.


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

`mime_type` can only be specified when expr is of BLOB/BFILE type.

***Example*** for Heap tables

```sql
DROP TABLE IF EXISTS to_clob;
CREATE TABLE to_clob(id int, blob_col blob);
INSERT INTO to_clob VALUES(2, '2D4E610062006300640087656500660067006800575B69006A006B00267B');

SELECT to_clob(blob_col, 2000) FROM to_clob WHERE id = 2;

TO_CLOB(BLOB_COL,2000)
----------------------------------------------------------------
Chinese abcd text efgh characters ijk
```

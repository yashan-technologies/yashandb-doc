```ebnf+diagram
to_clob::= TO_CLOB "(" expr ["," csid] ["," mime_type] ")"
```

TO_CLOB函数将[expr](../通用SQL语法/expr)数据源字符集转换为数据库字符集，并以CLOB值返回。

本函数仅支持基于HEAP表使用。

**expr**

通用表达式，需要被转换的数据。

当expr的值为NULL时，函数返回NULL。

**csid**

指定BLOB/BFILE数据的字符集ID。如果BLOB/BFILE数据的字符集与数据库当前字符集一致，则可以将csid的值指定为0，或完全省略csid。

仅当expr为BLOB/BFILE类型时，可以指定csid。


csid可配置范围如下：

|  字符集| csid值|
|:---------|:------|
| ASCII    | 1     |
| GBK      | 852   |
| UTF-8    | 873   |
| ISO88591 | 31    |
| UTF-16   | 2000  |
| GB18030  | 854   |


**mime_type**

该参数仅用于语法兼容，无实际意义。

仅当expr为BLOB/BFILE类型时，可以指定mime_type。

示例（HEAP表）

```sql
DROP TABLE IF EXISTS to_clob;
CREATE TABLE TO_CLOB(id INT, blob_col BLOB);
INSERT INTO to_clob VALUES(2, '2D4E610062006300640087656500660067006800575B69006A006B00267B');

SELECT TO_CLOB(blob_col, 2000) FROM to_clob WHERE id = 2;

TO_CLOB(BLOB_COL,2000)
----------------------------------------------------------------
中abcd文efgh字ijk符
```

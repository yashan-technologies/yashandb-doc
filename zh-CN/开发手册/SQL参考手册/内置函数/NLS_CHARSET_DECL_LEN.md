```ebnf
nls_charset_decl_len = NLS_CHARSET_DECL_LEN "(" byte_count "," char_set_id ")".
```

NLS_CHARSET_DECL_LEN函数返回字符类型列的声明长度，返回值为整型。

本函数遵循如下规则：

* 对于VARCHAR和CHAR、CLOB和NCLOB类型，直接返回BYTE_COUNT。
* 对于NCHAR和NVARCHAR类型，计算公式为FLOOR（BYTE_COUNT/单字符最大字节数），单字符最大字节数通过CHARSETID获得。

**byte_count**

指定字符集列的声明长度，其值须为INTEGER类型且不能为负数。

- 当BYTE_COUNT为NULL时，函数返回NULL。
- 当BYTE_COUNT为0时，函数返回0。

**char_set_id**

指定字符集的ID，其值须为NUMBER类型。

- 当CHAR_SET_ID为NULL时，函数返回null。
- 当CHAR_SET_ID为为0，负数值，无效值时，函数报错。

示例（HEAP表）

```sql
SQL> select nls_charset_decl_len(100,2000) from dual;

NLS_CHARSET_DECL_LEN(100,2000)
------------------------------
                            50

1 row fetched.

SQL> 
SQL> select nls_charset_decl_len(100,873) from dual;

NLS_CHARSET_DECL_LEN(100,873)
-----------------------------
                          100

1 row fetched.
```


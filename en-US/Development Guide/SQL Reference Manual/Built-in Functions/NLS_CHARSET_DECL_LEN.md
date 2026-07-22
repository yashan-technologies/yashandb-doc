```ebnf
nls_charset_decl_len = NLS_CHARSET_DECL_LEN "(" byte_count "," char_set_id ")".
```

The NLS_CHARSET_DECL_LEN function returns the declared length of character-type columns, the return value is integer type.

This function follows these rules:

* For VARCHAR, CHAR, CLOB, and NCLOB types, byte_count is returned directly.
* For NCHAR and NVARCHAR types, the calculation formula is FLOOR (byte_count / maximum bytes per character). The maximum bytes per character is obtained through charsetid.

**byte_count**

This parameter specifies the declared length of the character set column, its value must be of INTEGER type and cannot be a negative number.

- When BYTE_COUNT is NULL, the function returns NULL.
- When BYTE_COUNT is 0, the function returns 0.

**char_set_id**

This parameter specifies the ID of the character set. The value of this parameter must be of the NUMBER data type.

- When CHAR_SET_ID is NULL, the function returns NULL.
- When CHAR_SET_ID is 0, a negative value, or an invalid value, the fuction will report an error.

***Example*** for Heap tables

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


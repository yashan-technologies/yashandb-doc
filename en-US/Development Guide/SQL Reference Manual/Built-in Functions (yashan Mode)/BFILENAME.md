```ebnf+diagram
bfilename::= BFILENAME "(" dir_expr "," file_expr ")"
```

The BFILENAME function returns a BFILE locator, which corresponds to a physical binary file in the server file system.

If the function returns NULL, the client will adjust its return format to `BFILENAME(NULL)`.

**dir_expr**

dir_expr is a character type data, and its specification refers to [DIRECTORY](../SQL Statements (yashan Mode)/CREATE DIRECTORY).

When dir_expr is NULL, the function returns NULL.

**file_expr**

file_expr is a character type data, with a maximum length of 255 bytes.

When file_expr is NULL, the function returns NULL.

***Example*** for Heap tables

```sql
SELECT BFILENAME('MY_DIR','test.pdf') res FROM DUAL;
RES
--------------------------------
bfilename('MY_DIR','test.pdf')

SELECT BFILENAME(NULL, NULL) res FROM DUAL;
RES
--------------------------------
bfilename(NULL)
```

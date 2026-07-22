```ebnf
reverse = REVERSE "(" expr ")".
```

The REVERSE function is used to reverse the string `expr` from left to right. For example, `abc` will be reversed to `cba`.

This function is applicable only to HEAP tables.

**expr**

A general expression whose value must be of character types.

When the value of expr is NULL, the function returns NULL.

***Example*** for Heap tables

```sql
SELECT REVERSE('abc') FROM DUAL;

REVERSE('ABC')
--------------
cba

-- The REVERSE function performs reversal byte by byte​​, so the result of reversing multi-byte characters varies depending on the encoding method of the database's character set

-- Example 1: The character sets for both the database server and client are UTF8
SELECT USERENV('language') CHARACTER_SET , REVERSE('￥') REVERSE FROM DUAL;

CHARACTER_SET                                                    REVERSE
---------------------------------------------------------------- -------------------
UTF8                                                             ��

-- Example 2: The character sets for both the database server and client are GB18030
SELECT USERENV('language') CHARACTER_SET , REVERSE('￥') REVERSE FROM DUAL;

CHARACTER_SET                                                    REVERSE
---------------------------------------------------------------- -------------------
GB18030                                                          ぃ
```
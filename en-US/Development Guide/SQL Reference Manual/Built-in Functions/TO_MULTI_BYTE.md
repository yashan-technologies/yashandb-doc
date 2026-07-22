```ebnf
to_multi_byte = TO_MULTI_BYTE "(" char ")".
```

The TO_MULTI_BYTE function is used to convert half-width characters (single-byte characters) in the source string char to full-width characters (multi-byte characters). 

This function will only take effect when the [character set](../../../Database Administration/Instance Management/Database Character Set Configuration) configured for the database simultaneously includes both single-byte and multi-byte characters.

This function is applicable only to HEAP tables.

**char**

The source string, which must be of character type or another type that can be converted to character type.

- When char is of CHAR or VARCHAR type, the function returns data type VARCHAR; when char is of NCHAR or NVARCHAR type, it returns data type NVARCHAR.
- When char is of other types, it will first be implicitly converted to VARCHAR type and then processed by the function, returning data of type VARCHAR.
- If there are half-width characters in char that do not have corresponding full-width characters, the function will directly output the original character.
- If the full-width characters converted from char exceed the maximum length of VARCHAR, the function will truncate the string and return the maximum length full-width string.

***Example*** for Heap tables

```sql
SQL> select to_multi_byte('chi na12678976!?>@DEFHIG^') from dual;

TO_MULTI_BYTE('CHINA12678976!?>@DEFHIG^')
----------------------------------------------------------------
ｃｈｉ　ｎａ１２６７８９７６！？＞＠ＤＥＦＨＩＧＤ＾

1 row fetched.
```

```ebnf+diagram
to_single_byte::= TO_SINGLE_BYTE "(" char ")"
```

The TO_SINGLE_BYTE function is used to convert full-width characters (multi-byte characters) in the source string char to half-width characters (single-byte characters).

This function will only take effect when the [character set](../../All Manuals/Database Administration/Instance Management/Database Character Set Configuration) configured in the database includes both single-byte and multi-byte characters.

This function does not support vectorization calculations.

**char**

The source string, which must be of character type or other types that can be converted to character type.

- When char is of CHAR or VARCHAR type, the function returns a VARCHAR data type; if char is of NCHAR or NVARCHAR type, it returns an NVARCHAR data type.

- When char is of other types, it will be implicitly converted to VARCHAR type before the function processes it, and the function returns a VARCHAR type data.

- If a full-width character in char does not have a corresponding half-width character, the function will output the original character directly.

***Example*** for Heap tables

```sql
SELECT TO_SINGLE_BYTE('ｃｈｉ　ｎａ１２６７8976！？＞＠ＤＥＦＨＩＧ＾') FROM dual;

TO_SINGLE_BYTE('ＣＨＩ　ＮＡ１２６７8976！？＞＠
--------------------------------------------------------------
chi na12678976!?>@DEFHIG^

```

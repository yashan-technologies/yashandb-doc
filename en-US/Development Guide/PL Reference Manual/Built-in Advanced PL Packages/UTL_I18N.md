The UTL_I18N package is a set of services used to convert strings according to specified character sets, and performs localization processing based on character display differences between different regions globally.

## STRING_TO_RAW

```plsql
UTL_I18N.STRING_TO_RAW( 
 data IN VARCHAR2 CHARACTER SET ANY_CS,
 dst_charset IN VARCHAR2 DEFAULT NULL)
RETURN RAW;
```

This function converts a VARCHAR2 or NVARCHAR2 string to another character set, returning the result as a RAW type.

|Parameter |Description |
| ----------- | ---------------------------------------------- |
| data         | The VARCHAR2 or NVARCHAR2 string to be converted.                         |
| dst_charset  | The target character set; if empty, for char types, the default character set is the database character set; for nchar types, the default character set is the national character set. |
Note: If the user specifies an invalid character set or an empty string, this function will return an empty string.
***Example***

```plsql
SELECT utl_i18n.string_to_raw('abcdef', 'utf8') FROM dual;

UTL_I18N.STRING_TO_R                                             
---------------------------------------------------------------- 
616263646566                                                    

1 row fetched.
```

## RAW_TO_CHAR

```plsql
UTL_I18N.RAW_TO_CHAR(
 data IN RAW,
 src_charset IN VARCHAR2 DEFAULT NULL)
 RETURN VARCHAR2;
```

This function converts RAW data that is not encoded in the database character set into a VARCHAR2 string.

|Parameter |Description |
| ----------- |----------------------------|
| data         | The RAW type data to be converted.                           |
| src_charset  | Specifies the character set of the exported RAW data. If empty, CHAR types default to the database character set, and NCHAR types default to the national character set. |

If an invalid character set or an empty string is specified, this function will return an empty string.

***Example***

```plsql
SELECT utl_i18n.raw_to_char('616263646566', 'utf8') FROM dual;

UTL_I18N.RAW_TO_CHAR                                             
---------------------------------------------------------------- 
abcdef                                                          

1 row fetched.
```

## Exception Explanation

|Parameter |Description |
| ----------------------- | ---------------------------------- |
| dst_charset/src_charset | Returns NULL if an invalid character set not supported by yashan is provided |
| data                     | Raises an exception for a character set mismatch if the string does not meet character set requirements |

***Example***

```plsql
select UTL_I18N.STRING_TO_RAW('hello', 'JA16SJIS') from dual;

UTL_I18N.STRING_TO_R                                             
---------------------------------------------------------------- 

1 row fetched.

select UTL_I18N.RAW_TO_CHAR('8137A832', 'utf8') from dual;

YAS-00228 character set mismatch
```

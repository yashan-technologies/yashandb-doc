```ebnf+diagram
soundex::= SOUNDEX "(" expr ")"
```

The SOUNDEX function returns the soundex value of its parameter expr [general expression](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr). The expr must be of character type or another type that can be converted to character type. The function returns a string of type VARCHAR/NVARCHAR. The soundex value is a special abbreviation of words based on their English pronunciation.

This function follows these rules:

- This function does not support vectorization calculation.
- When expr is of type NCHAR, NVARCHAR, or NCLOB, the return value is of type NVARCHAR; when expr is of other types, the return value is of type VARCHAR.
- The expr cannot be BFILE or LOB type data exceeding 65534 bytes.
- If the value of expr, after implicit conversion to character type, does not contain English characters, the function returns NULL.
- If expr is NULL, the function returns NULL.

***Example*** for Heap tables

```sql
SELECT SOUNDEX(NULL) FROM dual;

SOUNDEX(NULL)
------------- 

SELECT SOUNDEX('SSHS') FROM dual;

SOUNDEX('SSHS')
--------------- 
       S200
SELECT SOUNDEX('SMYTHE') FROM dual;

SOUNDEX('SMYTHE')
----------------- 
       S530
SELECT SOUNDEX('SMITH') FROM dual;

SOUNDEX('SMITH')
---------------- 
       S530  
```

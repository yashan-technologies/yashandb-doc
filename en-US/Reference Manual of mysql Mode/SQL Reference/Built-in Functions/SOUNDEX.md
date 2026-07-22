```ebnf
soundex = SOUNDEX "(" expr ")".
```

The SOUNDEX function returns the [soundex code](https://www.archives.gov/research/census/soundex) of its parameter expr [general expression](../General SQL Syntax/expr). The expr must be of character type or another type that can be converted to character type.  


This function follows these rules to convert the input value:

1. Locate the first Chinese character, English letter, or special symbol in the string as the starting conversion position, and use the original character as the first character of the return value.

2. Convert English letters in the string according to the conversion rules in the table below, and use them as subsequent characters in the return value; all other characters will be ignored.

   |Letters|Code|
   |------|----|
   |A, E, I, O, U, H, W, Y| 0 |
   |B, F, P, V| 1 |
   |C, G, J, K, Q, S, X, Z| 2 |
   |D, T|3|
   |L |4|
   |M, N|5|
   |R|6|

3. Remove duplicate consecutive numeric parts in the converted string, keep only one instance, delete all zeros, and finally return the first 4 characters.

4. Only the first 4 characters are returned; if there are fewer than 4 characters, zeros are appended at the end.


This function follows these rules:

- When expr is of type RAW, BLOB, or BIT, the return value is of type RAW; When expr is of type TEXT, the return value is of type TEXT; when expr is of other types, the return value is of type VARCHAR.
- If the value of expr, after implicit conversion to character type, does not contain any characters that satisfy the conversion rules, the function returns NULL.
- If expr is NULL, the function returns NULL.

***Example*** for Heap tables

```sql
SELECT SOUNDEX(NULL) from dual;

SOUNDEX(NULL)
------------- 

SELECT SOUNDEX('SSHS') from dual;

SOUNDEX('SSHS')
--------------- 
       S200
SQL> SELECT SOUNDEX('SMYTHE') from dual;

SOUNDEX('SMYTHE')
----------------- 
       S530
SELECT SOUNDEX('SMITH') from dual;

SOUNDEX('SMITH')
---------------- 
       S530  
```

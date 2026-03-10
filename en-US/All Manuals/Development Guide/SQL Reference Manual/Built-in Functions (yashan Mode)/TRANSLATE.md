```ebnf+diagram
translate::= TRANSLATE ("(" expr "," from_string "," to_string ")" | "(" USING (CHAR_CS | NCHAR_CS) ")")
```

The TRANSLATE function has the following usages:

- Usage 1: It is used to replace each character in the from_string with the corresponding character in the to_string, one-to-one, from the source string [expr](../General SQL Syntax/expr), returning a new string.

- Usage 2: The TRANSLATE…USING usage, where the TRANSLATE function is used to perform character set conversion on the source string expr according to the specified encoding method, returning a string of the corresponding type.

This function adheres to the following rules:

- This function does not support vectorization calculation.

- Usage 1 also follows these rules:
  
    - The from_string is case-sensitive when matching with the source string expr.

    - When the from_string is longer than the to_string, every character in the exceeding part of the from_string will be directly deleted from the new string.

    - When expr is of type NCLOB/NCHAR/NVARCHAR, the return value will be of NVARCHAR type, while in other scenarios, the return value will be of VARCHAR type.

**expr**

A general expression whose value must be of character type or other types that can be converted to character type.

- expr cannot be BFILE or LOB type data exceeding 65534 bytes.

- When the value of expr is NULL, the function returns NULL.

**from_string**

The string to be replaced, from_string is a general expression that must be character type data or other types that can be converted to character type (LOB types support implicit conversion).

When the value of from_string is NULL, the function returns NULL.

**to_string**

The string to replace with, replacements are made one-to-one according to this string, to_string is a general expression that must be character type data or other types that can be converted to character type (LOB types support implicit conversion).

When the value of to_string is NULL, the function returns NULL.

***Example*** for Heap tables

```sql
-- One-to-one replacement, replacing 'c' with 'a' and 'h' with 'b'
SELECT TRANSLATE('I am chinese', 'ch', 'ab') TRANSLATE FROM DUAL;
TRANSLATE
-------------
I am abinese

-- 'china' is longer than 'ab', the exceeding characters 'i', 'n', and 'a' will be directly deleted from the new string
SELECT TRANSLATE('I am chinese', 'china', 'ab') TRANSLATE FROM DUAL;
TRANSLATE
-------------
I m abese
```

**translate_using**

Used to specify the string encoding method:

- CHAR_CS: Indicates that expr is converted into a string encoded with the database character set, the return value of the function will be of VARCHAR type.

- NCHAR_CS: Indicates that expr is converted into a string encoded with the national character set, the return value of the function will be of NVARCHAR type.

***Example*** for Heap tables

```sql
SELECT TRANSLATE('Yashan Database' USING CHAR_CS) TRANSLATE
FROM DUAL;
TRANSLATE
-----------------
Yashan Database

SELECT TRANSLATE('Yashan Database' USING NCHAR_CS) TRANSLATE
FROM DUAL;
TRANSLATE
----------------------------------------------------------------
Yashan Database
```

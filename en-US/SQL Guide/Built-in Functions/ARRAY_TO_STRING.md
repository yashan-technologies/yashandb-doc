```ebnf+diagram
array_to_string::= ARRAY_TO_STRING "(" array_var "," split_string [ "," replace_string ] ")"
```

The ARRAY_TO_STRING function concatenates the elements of an array (array_var) using split_string as the delimiter. If there are NULL elements in the array and replace_string is not empty, the NULL elements will be replaced with replace_string before concatenation. The function returns a string of type VARCHAR.

This function does not support vectorization calculation.

**array_var**

An initialized array variable, which cannot be NULL.

The elements of array_var (when array_var is a multi-dimensional nested array, the elements are the innermost array elements) must be of character type or other types that can be implicitly converted to character type.

**split_string**

[General Expression](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr), which must be of character type or other types that can be implicitly converted to character type. If split_string is NULL, there will be no delimiter in the concatenation.

**replace_string**

[General Expression](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr), which must be of character type or other types that can be implicitly converted to character type.

***Example*** for Heap tables

```sql
CREATE OR REPLACE TYPE a2s_type IS VARRAY(20) OF VARCHAR(20);
/

SELECT ARRAY_TO_STRING(a2s_type('aaa','bbb','ccc'), ' ') res FROM DUAL;
RES                                                              
---------------------------------------------------------------- 
aaa bbb ccc  

SELECT ARRAY_TO_STRING(a2s_type('aaa','bbb', NULL, 'ccc'), ',') res FROM DUAL;
RES                                                              
---------------------------------------------------------------- 
aaa,bbb,ccc  

SELECT ARRAY_TO_STRING(a2s_type('aaa','bbb', NULL, 'ccc'), ',', 'NULL') res FROM DUAL;
RES                                                              
---------------------------------------------------------------- 
aaa,bbb,NULL,ccc    

SELECT ARRAY_TO_STRING(a2s_type('aaa','bbb', NULL, 'ccc'), NULL) res FROM DUAL;
RES                                                              
---------------------------------------------------------------- 
aaabbbccc                                                       

SELECT ARRAY_TO_STRING(a2s_type('aaa','bbb', NULL, 'ccc'), ',', NULL) res FROM DUAL;
RES                                                              
---------------------------------------------------------------- 
aaa,bbb,ccc  
```

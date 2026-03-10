```ebnf+diagram
initcap::= INITCAP "(" expr ")"
```

The INITCAP function splits the string represented by [expr](../General SQL Syntax/expr) into words and converts the initial letters of the separated words to uppercase, while the other letters are converted to lowercase.

The function considers characters other than English letters and numeric characters as delimiters and separates words accordingly, including punctuation marks, ordinary symbols, control characters, Greek letters, and Chinese characters.

**expr**

The value of expr must be of character type or other types that can be converted to character type.

- In the vectorized execution engine, expr cannot be LOB type data stored out-of-line.

- expr cannot be BFILE or LOB type data exceeding 65534 bytes.

- When expr is NCLOB/NCHAR/NVARCHAR, the return value is of NVARCHAR type; in other cases, the return value is of VARCHAR type.

- When the value of expr is NULL, the function returns NULL.

***Example***

```sql
SELECT INITCAP('1wWork who i——s i1w') res FROM DUAL;
RES
---------------------
1wwork Who I——S I1w 

SELECT INITCAP(SYSDATE||':today') res FROM DUAL;
RES                                                              
---------------------------------------------------------------- 
2022-11-08 02:41:17:Today   

-- Convert floating-point data to character type first
SELECT INITCAP(CAST('inf' AS FLOAT)||'/'||CAST('1.11' AS FLOAT)) res FROM DUAL;
RES                   
--------------------- 
Inf/1.11000001e+000    

-- Convert binary data to character type first
SELECT INITCAP(b'010101') res FROM DUAL;
RES 
------------------ 
21    
```

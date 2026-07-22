```ebnf
concat_ws = CONCAT_WS "(" separator "," expr {"," expr} ")".
```

The CONCAT_WS function concatenates the values of multiple [expr](../General SQL Syntax/expr) using the separator, returning a concatenated string.

This function follows these rules:

- The number of parameters for this function must be [2,32768].
- If the expr contains NCHAR or NVARCHAR type data, the return type will be NVARCHAR; otherwise, it will be VARCHAR.
- When there is only one non-empty parameter (excluding the separator), the function returns that non-empty parameter.
- If the string to be concatenated is NULL or '', the function does not concatenate that string (and does not concatenate the separator consecutively).
- If the separator is NULL or '', the function returns NULL.

**separator**

The separator must be of character type or string but cannot be BFILE or LOB types that exceed 65534 bytes.

**expr**

The data to be concatenated can be numeric, character, boolean, or datetime types.

When the value of expr is of CHAR type, the function will first pad spaces to its defined length before participating in the concatenation.

***Example***

```sql
SELECT CONCAT_WS('1','2', '', 'ab', 'dc') res FROM DUAL;
RES   
----- 
21ab1dc 

SELECT CONCAT_WS(12,null,null,'','a',null) res FROM DUAL;
RES   
----- 
a  
```

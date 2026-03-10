```ebnf+diagram
lower::= LOWER "(" expr ")"
```

The LOWER function converts uppercase letters in the value of [expr](../General SQL Syntax/expr) to lowercase and returns a new string.

**expr**

A general expression whose value must be of character type or other types that can be converted to character type.

- In the vectorized execution engine, expr cannot be LOB type out-of-line storage data.

- expr cannot be BFILE or LOB type data larger than 65534 bytes.

- When expr is of CHAR, NCHAR, or NVARCHAR type, the return value is of the same type as expr; in other cases, the return value is of VARCHAR type.

- When the value of expr is NULL, the function returns NULL.

***Example***

```sql
SELECT LOWER('Shenzhen NIHAO') l1, LOWER(NULL) l2 FROM DUAL;
L1            L2    
------------- ----- 
shenzhen nihao       
```

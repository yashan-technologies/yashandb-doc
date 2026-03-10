```ebnf+diagram
collation::= CRC32 "(" expr ")"
```

The CRC32 function returns the cyclic redundancy check value of the given parameter [expr](../General SQL Syntax/expr), which is a 32-bit unsigned number.

**expr**

- The type of expr is character type, or convertible to character type.

- If the value of expr is NULL, the function returns NULL.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT CRC32('yashan') res FROM DUAL;
res                   
--------------------- 
3491700740
    
SELECT CRC32(123) res FROM DUAL;
res       
--------- 
2286445522 
```

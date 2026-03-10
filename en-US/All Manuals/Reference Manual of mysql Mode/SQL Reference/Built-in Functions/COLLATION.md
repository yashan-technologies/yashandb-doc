```ebnf+diagram
collation::= COLLATION "(" expr ")"
```

The COLLATION function returns the collation of the value of the given parameter [expr](../General SQL Syntax/expr). When the input parameter's value is of a non-string type, it returns "binary"; when the input parameter's value is of a string type, it returns the default collation of the server's character set.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT COLLATION('yashan') res FROM DUAL;
res                   
--------------------- 
utf8mb4_general_ci
    
SELECT COLLATION(123) res FROM DUAL;
res       
--------- 
binary 
```

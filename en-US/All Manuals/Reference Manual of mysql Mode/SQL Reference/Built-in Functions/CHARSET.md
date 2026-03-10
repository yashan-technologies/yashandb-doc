```ebnf+diagram
charset::= CHARSET "(" expr ")"
```

The CHARSET function returns the character set of the given parameter [expr](../General SQL Syntax/expr). When the input parameter is of a non-string type, it returns "binary"; when the input parameter is of a string type, it returns the server character set.

***Example*** for  Heap tables

```sql
SELECT CHARSET('yashan') res FROM DUAL;
res       
--------- 
utf8mb4  
    
SELECT CHARSET(123) res FROM DUAL;
res       
--------- 
binary 
```

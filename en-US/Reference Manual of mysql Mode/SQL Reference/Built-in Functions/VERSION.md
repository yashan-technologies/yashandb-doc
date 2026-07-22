```ebnf
version = VERSION "("")".
```

The VERSION function returns the version information of the current YaShanDB database that is compatible with MySQL.

This function has no parameters, and the return value is of VARCHAR type.

***Example*** for  Heap tables

```sql
SELECT VERSION() res FROM DUAL;
res       
--------- 
5.7.42
```

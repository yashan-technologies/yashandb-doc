```ebnf
database = DATABASE "("")".
```

The DATABASE function returns the current database name.

This function takes no parameters and returns a value of type VARCHAR.

***Example*** for Standalone Deployment Heap tables

```sql
USE SYS;
SELECT DATABASE() res FROM DUAL;
res                                                              
---------------------------------------------------------------- 
SYS
```

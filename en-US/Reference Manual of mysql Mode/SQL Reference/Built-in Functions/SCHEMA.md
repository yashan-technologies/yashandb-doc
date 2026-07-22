```ebnf
schema = SCHEMA "("")".
```

The SCHEMA function returns the name of the current database and is a synonym of the [DATABASE](DATABASE) function.

This function has no input parameters and returns a value of VARCHAR type.

***Example*** for  Heap tables

```sql
USE SYS;
SELECT SCHEMA() res FROM DUAL;
res                                                              
---------------------------------------------------------------- 
SYS
```

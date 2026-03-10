```ebnf+diagram
user::= USER "("")"
```

The USER function returns a string combination of the username and hostname used by the client to connect to the database.

This function has no parameters and returns a value of VARCHAR type.

***Example*** for  Heap tables

```sql
SELECT USER() res FROM DUAL;
res               
----------------- 
SYS@localhost
```

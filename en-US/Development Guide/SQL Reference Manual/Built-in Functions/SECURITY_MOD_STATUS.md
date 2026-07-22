```ebnf
SECURITY_MOD_STATUS = SECURITY_MOD_STATUS"()" .
```

SECURITY_MOD_STATUS is used to retrieve and display the current security mode.

***Example***

```sql
select SECURITY_MOD_STATUS() from dual;

SECURITY_MOD_STATUS(  
--------------------- 
Approved Mode Status         

1 row fetched.
```

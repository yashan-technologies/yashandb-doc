The DBMS_ASSERT package provides a set of built-in stored procedures/functions for validating the properties of input values.

> **Caution**:
>
> - The DBMS_ASSERT advanced package is not suitable for ISC Distributed Cluster Deployment.

## ENQUOTE_LITERAL

```plsql
DBMS_ASSERT.ENQUOTE_LITERAL(str VARCHAR)RETURN VARCHAR
```

This function will escape the input string using single quotes and return the escaped string. The specific escaping rules are as follows:

- It verifies whether single quotes in the input string are paired with adjacent single quotes; if they are not paired, an error will be raised.

- It checks whether the input string has already been escaped with single quotes; if so, it will not escape again and will directly return the original data.

|Parameter |Description |
| -------- | ------------------------------------------------------------ |
| STR       | The string to be escaped                                |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
SQL> SELECT DBMS_ASSERT.ENQUOTE_LITERAL('TEST') FROM DUAL;

DBMS_ASSERT.ENQUOTE_LITERAL('TEST')                              
---------------------------------------------------------------- 
'TEST'                                                          

1 row fetched.
```

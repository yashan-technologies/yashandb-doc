```ebnf+diagram
localtime::= LOCALTIME ["(" [integer] ")"]
```

The LOCALTIME function returns the current SCN as per the operating system settings of the database. Its return type is TIMESTAMP, and it adheres to the format specified by the TIMESTAMP_FORMAT parameter.

This function does not support vectorization calculation.

LOCALTIME has the following three forms:

- `LOCALTIME`
- `LOCALTIME()`
- `LOCALTIME(integer)`, where the integer must be a literal between 0 and 9, representing the number of microseconds to retain, with discarded digits rounded.

If multiple LOCALTIME functions appear in a SQL statement, the function will be called only once during the execution of that statement, ensuring that all LOCALTIME functions return the same SCN value.

When the LOCALTIME function participates in calculations, its calculation rules are consistent with the TIMESTAMP type, as described in the [Arithmetic Operators](../../All Manuals/Development Guide/SQL Reference Manual/Operators/Arithmetic Operators) section.

***Example*** for Heap tables

```sql
SELECT LOCALTIME res1,LOCALTIME() res2,LOCALTIME(9) res3 ,LOCALTIME+1 res4 FROM DUAL;
RES1                                                             RES2                                                             RES3                                                             RES4                             
---------------------------------------------------------------- ---------------------------------------------------------------- ---------------------------------------------------------------- -------------------------------- 
2023-05-03 20:35:30.152249                                       2023-05-03 20:35:30.152249                                       2023-05-03 20:35:30.152249                                       2023-05-04                      
```

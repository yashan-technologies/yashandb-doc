```ebnf+diagram
localtimestamp::= LOCALTIMESTAMP ["(" [integer] ")"]
```

The LOCALTIMESTAMP function returns the SCN corresponding to the session-set time zone, with a return type of TIMESTAMP, which is consistent with the format specified by the TIMESTAMP_FORMAT parameter.

This function does not support vectorization calculation.

LOCALTIMESTAMP has the following three forms:

- `LOCALTIMESTAMP`
- `LOCALTIMESTAMP()`
- `LOCALTIMESTAMP(integer)`, where integer must be a literal between 0 and 9, indicating the number of microseconds to retain. The discarded digits are rounded off.

If multiple LOCALTIMESTAMP functions appear in a SQL statement, the function will be called only once during the execution of that statement, ensuring that multiple LOCALTIMESTAMP functions return the same SCN value.

When the LOCALTIMESTAMP function participates in calculations, its calculation rules are consistent with the TIMESTAMP type, as described in the [Arithmetic Operators](../../All Manuals/Development Guide/SQL Reference Manual/Operators/Arithmetic Operators) section.

***Example*** for Heap tables

```sql
SELECT LOCALTIMESTAMP res1,LOCALTIMESTAMP() res2,LOCALTIMESTAMP(9) res3,LOCALTIMESTAMP+1 res4 FROM DUAL;
RES1                                                             RES2                                                             RES3                                                             RES4                             
---------------------------------------------------------------- ---------------------------------------------------------------- ---------------------------------------------------------------- -------------------------------- 
2023-05-03 20:38:06.665584                                       2023-05-03 20:38:06.665584                                       2023-05-03 20:38:06.665584                                       2023-05-04                      
```

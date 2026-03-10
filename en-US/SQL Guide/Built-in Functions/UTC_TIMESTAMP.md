```ebnf+diagram
utc_timestamp::= UTC_TIMESTAMP ["(" [integer] ")"]
```

The UTC_TIMESTAMP function returns the current Coordinated Universal Time set by the operating system where the database is located. Its return type is TIMESTAMP and is consistent with the format specified by the TIMESTAMP_FORMAT parameter.

UTC_TIMESTAMP has the following three forms:

- `UTC_TIMESTAMP`
- `UTC_TIMESTAMP()`
- `UTC_TIMESTAMP(integer)`, where integer must be an integer literal between 0 and 9, representing the number of microseconds retained, with discarded digits rounded.

If multiple UTC_TIMESTAMP functions appear in a SQL statement, the function will only be called once during the execution of that statement, ensuring that multiple UTC_TIMESTAMP functions return the same SCN value.

When the UTC_TIMESTAMP function is involved in calculations, its calculation rules are consistent with the TIMESTAMP type, as described in the [Arithmetic Operators](../../All Manuals/Development Guide/SQL Reference Manual/Operators/Arithmetic Operators) section.

***Example***

```sql
SELECT UTC_TIMESTAMP res1,UTC_TIMESTAMP() res2,UTC_TIMESTAMP(9) res3 FROM DUAL;
RES1                                                             RES2                                                             RES3                                                             
---------------------------------------------------------------- ---------------------------------------------------------------- ---------------------------------------------------------------- 
2023-05-04 06:24:04.636637                                       2023-05-04 06:24:04.636637                                       2023-05-04 06:24:04.636637                                      
```

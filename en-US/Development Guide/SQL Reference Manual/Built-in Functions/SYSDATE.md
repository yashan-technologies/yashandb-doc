```ebnf
sysdate = SYSDATE ["(" [integer] ")"].
```

The SYSDATE function returns the current date set by the operating system where the database is located. Its return type is DATE and it is consistent with the format specified by the DATE_FORMAT parameter.

The following three forms of SYSDATE are synonymous, and the return results are indistinguishable:

- `SYSDATE`
- `SYSDATE()`
- `SYSDATE(integer)`, where integer must be an integer literal between 0 and 6.

If multiple SYSDATE functions appear in a SQL statement, the function will only be called once during the execution of that statement, ensuring that multiple SYSDATE functions return the same date value.

***Example***

```sql
SELECT SYSDATE res1,SYSDATE() res2,SYSDATE(5) res3 FROM DUAL;
RES1                    RES2                     RES3                   
----------------------- ------------------------ ----------------------
2022-11-02 03:07:43     2022-11-02 03:07:43      2022-11-02 03:07:43   
```

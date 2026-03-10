```ebnf+diagram
now::= NOW ["()"]
```

The NOW function returns the current date set by the operating system of the database without any given parameters. Its return type is DATE and is consistent with the format specified by the DATE_FORMAT parameter.

If multiple NOW functions appear in a SQL statement, the NOW function will be called each time during the execution of that statement to retrieve the current date.

***Example***

```sql
SELECT NOW FROM DUAL;
NOW                              
-------------------------------- 
2022-08-24 23:55:31 
```

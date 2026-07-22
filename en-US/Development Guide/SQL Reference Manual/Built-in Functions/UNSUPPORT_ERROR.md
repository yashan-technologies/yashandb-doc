```ebnf
unsupport_error = UNSUPPORT_ERROR "("")".
```

The UNSUPPORT_ERROR function returns the error message This statement is temporarily unable to execute due to an internal error. 

The following two forms of UNSUPPORT_ERROR are synonymous, and the return results are the same. No parameters are allowed within the parentheses:

- `UNSUPPORT_ERROR`
- `UNSUPPORT_ERROR()`

***Example***

```sql
SELECT UNSUPPORT_ERROR() FROM DUAL;

[1:8]YAS-04349 This statement is temporarily unable to execute due to an internal error
```

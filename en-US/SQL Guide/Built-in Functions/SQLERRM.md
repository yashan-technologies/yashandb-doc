```ebnf+diagram
sqlerrm::= SQLERRM "("[option errCode]")"
```

SQLERRM is used to return the error description corresponding to the error code.

* SQLERRM is a function that accepts either 0 or 1 parameter.
* SQLERRM can only be used in the body of a procedure and not in regular SQL statements. The following is an incorrect usage:
    ```sql
    INSERT INTO TABLE VALUES(sqlErrm)
    ```
* SQLERRM outside of the exception handler, when there are no parameters, always returns "YAS-00000 normal, successful completion"; when there is a parameter, it matches the error code information based on the parameter.
* SQLERRM in the exception handler, if no parameters are set, returns the error message corresponding to the current error code and removes format characters; if parameters are set, it returns the error message corresponding to the parameter and removes format characters; however, the parameter set may not necessarily be one that exists internally or is user-defined, so further refinement is possible.

***Example***

```plsql
DECLARE
BEGIN
    DBMS_OUTPUT.PUT_LINE(SQLERRM);
END;
/

--result
YAS-00000 normal, successful completion
```

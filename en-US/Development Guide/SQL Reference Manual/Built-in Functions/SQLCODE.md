```ebnf
sqlcode = SQLCODE ["("")"].
```

The SQLCODE function is used to return the current error code.

* SQLCODE is a function that takes no parameters and can be written as `SQLCODE` or `SQLCODE()`.
* SQLCODE can only be used within the body of a procedure and cannot be used in ordinary SQL statements. The following is an incorrect usage:
    ```sql
    insert into table values(sqlCode)
    ```
* SQLCODE returns the current error code value in the exception handler of a procedure body; outside of the exception handler, it returns 0.

***Example***

```plsql
DECLARE
	past_due EXCEPTION;
	PRAGMA EXCEPTION_INIT (past_due, 30103);
BEGIN
    RAISE past_due;

	EXCEPTION
	WHEN past_due THEN 
		DBMS_OUTPUT.PUT_LINE(SQLCODE);
END;
/

--result
30103
```

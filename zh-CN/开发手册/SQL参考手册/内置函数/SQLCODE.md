```ebnf
sqlcode = SQLCODE ["("")"].
```

SQLCODE函数是用来返回当前的错误码。

*   SQLCODE是一个函数，没有参数，可以写为`SQLCODE`及`SQLCODE()`。
*   SQLCODE只能用在过程体中，不能用于普通SQL语句中，下面为错误用法：
    ```sql
    insert into table values(sqlCode)
    ```
*   SQLCODE在过程体的异常句柄中，得到的是当前错误码值；在异常句柄之外，则返回的是0。

示例

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

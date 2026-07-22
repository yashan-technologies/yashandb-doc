```ebnf
sqlerrm = SQLERRM "("[option errCode]")".
```

SQLERRM用来返回错误码对应的错误描述信息。

*   SQLERRM是一个函数，其参数个数为0个或者1个。
*   SQLERRM只能用在过程体中，不能用于普通sql语句中，下面为错误用法：
    ```sql
    insert into table values(sqlErrm)
    ```
*   SQLERRM在异常句柄之外，当没有参数时，返回的都是 "YAS-00000 normal, successful completion"；有参数时，则根据参数去匹配错误码信息。
*   SQLERRM在异常句柄中，如果没有设置参数，则返回当前错误码对应的错误信息，同时会去掉格式符；如果设置了参数，则会返回参数对应的错误信息，并去掉格式符；但是设置的参数不一定是内部有的或者用户自定义的，因此可继续细化。

示例

```plsql
DECLARE
BEGIN
    DBMS_OUTPUT.PUT_LINE(SQLERRM);
END;
/

--result
YAS-00000 normal, successful completion
```

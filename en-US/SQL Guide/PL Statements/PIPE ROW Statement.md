The PIPE ROW statement is used within pipeline functions to produce a single row of data and write it into the pipeline. The format is as follows:

_PIPE ROW(expr);_

Here, expr is the row data expression, and its result type must match the element type of the return collection specified in the pipeline function definition.

> **Note**:
>
> If the pipeline function is in an autonomous transaction, a COMMIT or ROLLBACK must be executed before executing PIPE ROW.

***Example***

```plsql
CREATE OR REPLACE TYPE ya_pipe_func_typ1 IS TABLE OF INT;
/

CREATE OR REPLACE FUNCTION ya_pipe_func(p1 INT) RETURN ya_pipe_func_typ1 PIPELINED IS
BEGIN
    FOR i IN 1..p1 LOOP
      pipe row(i);
    END LOOP;
    RETURN;
END;
/
```

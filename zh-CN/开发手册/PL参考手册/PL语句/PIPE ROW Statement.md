PIPE ROW语句在管道函数中使用，产生一行数据并写入管道中。格式为：

_PIPE ROW(expr);_

其中，expr为行数据表达式，其结果类型必须与管道函数定义指定的返回集合元素类型匹配。

> **Note**:
>
> 如果管道函数在自治事务中，在执行PIPE ROW之前必须先执行COMMIT或ROLLBACK。

示例

```plsql
CREATE OR REPLACE TYPE ya_pipe_func_typ1 IS TABLE OF INT;
/

CREATE OR REPLACE FUNCTION ya_pipe_func(p1 int) RETURN ya_pipe_func_typ1 PIPELINED IS
BEGIN
    for i in 1..p1 loop
      pipe row(i);
    end loop;
    RETURN;
END;
/
```

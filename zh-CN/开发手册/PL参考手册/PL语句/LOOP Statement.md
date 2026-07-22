LOOP Statement为循环控制语句，其语法形式为：

_loop_

_......_

_end loop \[label\_name\];_

其中，label_name为可选项，作为一个标签（Label）标识循环体，提升可读性，使用label_name需在循环体开始处有相匹配的标签定义，否则将报错。

LOOP Statement无条件进入循环语句，并在结束后再次返回到循环语句的顶部循环执行，除非存在顺序控制语句或引发异常退出循环，因此，在循环语句里必须存在[GOTO Statement](./GOTO Statement)、[EXIT Statement](./EXIT Statement)、[CONTINUE Statement](./CONTINUE Statement)等顺序控制语句，否则将导致执行进入死循环。

示例

```plsql
DECLARE
i int;
BEGIN
    i := 0;
    <<loop1>>
    LOOP
        i := i + 1;
        IF i=3 THEN
            GOTO a;
        END IF;
        DBMS_OUTPUT.PUT_LINE ('This is: '||i);
    END LOOP loop1;
    <<a>>
    DBMS_OUTPUT.PUT_LINE ('The End');
END;
/
 
--result
This is: 1
This is: 2
The End
```

示例

```plsql
DECLARE
a int :=1;
BEGIN
    LOOP
        DBMS_OUTPUT.PUT_LINE('loop:'||a);
        a := a+1;
        EXIT WHEN a > 2;
    END LOOP;
END;
/

--result
loop:1
loop:2
```

WHILE Statement为循环控制语句，其语法形式为：

_while condition loop_

_......_

_end loop \[label_name\];_

其中，condition为能产生布尔结果的表达式。

label_name为可选项，作为一个标签（Label）标识循环体，提升可读性，使用label_name需循环体开始处需有相匹配的标签定义，否则将报错。

WHILE Statement首先判断condition的结果，为true则进入循环语句，并在结束后再次进行判断condition的结果，直到condition的结果为false时结束循环语句，因此：

*   在WHILE Statement前，应该保证condition的结果为true，否则无法进入循环语句执行。
*   在循环语句里，必须存在使condition结果为false的语句，或者存在[GOTO Statement](./GOTO Statement)、[EXIT Statement](./EXIT Statement)、[CONTINUE Statement](./CONTINUE Statement)等顺序控制语句，否则将导致执行进入死循环。

示例

```plsql
DECLARE
    i INT;
BEGIN
    i := 0;
    WHILE i < 5 LOOP
        i := i + 1;
        IF i=3 THEN
            GOTO a;
        END IF;
        DBMS_OUTPUT.PUT_LINE ('This is: '||i);
    END LOOP;
    <<a>>
    DBMS_OUTPUT.PUT_LINE ('The End');
END;
/
 
--result
This is: 1
This is: 2
The End



CREATE OR REPLACE PROCEDURE ya_proc(b INT) IS
    a INT := 1;
BEGIN
    WHILE a<=b LOOP
        DBMS_OUTPUT.PUT_LINE ('while:' || a);
        a := a+1;
    END LOOP;
END;
/

exec ya_proc(3);

--result
while:1
while:2
while:3
```

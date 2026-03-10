CONTINUE Statement为顺序控制语句，其语法形式为：

_CONTINUE \[label_name\] \[WHEN condition\];_

其中，label_name为可选项，标识需跳过迭代的当前或外层循环；WHEN condition为可选项，condition为能产生布尔结果的表达式。

CONTINUE语句用于跳过循环的当前迭代，跳转到循环开始处，进行下一次循环的执行。需注意：

* label_name标识需跳过迭代的当前或外层循环，若label_name对应的标签不标识当前或外层循环，或为不存在等非法情况，将报错处理。label_name可省略，则CONTINUE语句执行时跳过当前循环的当前迭代。

* WHEN condition子句中的条件结果用于确定是否执行CONTINUE语句，true为执行，false为不执行。当省略WHEN condition子句时，默认执行CONTINUE语句。

示例

```plsql
DECLARE
i INT;
BEGIN
    i := 0;
    <<loop1>>
    WHILE i < 3 LOOP
        i := i + 1;
        CONTINUE loop1 WHEN i = 2;
        DBMS_OUTPUT.PUT_LINE ('This is: '||i);
    END LOOP loop1;
    DBMS_OUTPUT.PUT_LINE ('The End');
END;
/
 
--result
This is: 1
This is: 3
The End

DECLARE
    a INT;
BEGIN
    a := 1;
    LOOP
        a :=a + 1;
        DBMS_OUTPUT.PUT_LINE('loop1');
        CONTINUE WHEN a < 5;
        DBMS_OUTPUT.PUT_LINE('loop2');
        EXIT;
    END LOOP;
END;
/

--result
loop1
loop1
loop1
loop1
loop2
```

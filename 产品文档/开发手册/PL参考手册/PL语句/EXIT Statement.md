EXIT Statement为顺序控制语句，其语法形式为：

_EXIT \[label_name\] \[WHEN condition\]；_

其中，label_name为可选项，标识需退出的当前或外层循环；WHEN condition为可选项，condition为能产生布尔结果的表达式。

EXIT语句用于退出循环，跳转到循环结束处，继续向下执行。需注意：

* label_name标识需退出的当前或外层循环，若label_name对应的标签不标识当前或外层循环，或为不存在等其他非法情况，将报错处理。label_name可省略，则EXIT语句执行时退出当前循环。

* WHEN condition子句中的条件结果确定是否执行EXIT语句，true为执行，false为不执行。若省略WHEN condition，默认将执行EXIT语句。
* EXIT语句只能出现在循环中，包括[WHILE Statement](./WHILE Statement)、[FOR Statement](./FOR Statement)和[LOOP Statement](./LOOP Statement)。

示例

```plsql
DECLARE
i INT;
BEGIN
i := 0;
<<loop1>>
LOOP
i := i + 1;
EXIT loop1 WHEN i = 3;
DBMS_OUTPUT.PUT_LINE ('This is: '||i);
END LOOP loop1;
DBMS_OUTPUT.PUT_LINE ('The End');
END;
/
 
--result
This is: 1
This is: 2
The End
```

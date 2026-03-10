FOR Statement为循环控制语句，其语法形式分为普通用法和for cursor用法两种。

普通用法
-------

_for counter in \[reverse\] lower_bound .. upper_bound loop_

_......_

_end loop \[label_name\];_

其中，counter为计数器，lower_bound为计数器下限值，upper_bound为计数器上限值；reverse为控制参数，使用此参数表示将计数器从默认的递增改为递减模式。

counter、lower_bound、upper_bound均为BIGINT类型，取值范围同BIGINT值域。

label_name为可选项，作为一个标签（Label）标识循环体，提升可读性，使用label_name需在循环体开始处有相匹配的标签定义，否则将报错。

### counter

counter为FOR Statement里隐式声明的局部变量，只能在FOR语句内部使用，不可被继承类型，退出FOR语句后失效。

counter可以与过程体全局变量、或者其他循环控制语句内部的局部变量重名，此时 ：

*   在FOR Statement内部，counter表示的是本语句局部变量，且只能由_for counter in \[reverse\] lower_bound .. upper_bound loop_对其赋值。
*   当需要在FOR Statement内部访问其他的同名变量时，必须使用标签.counter来识别，标签的描述参见[GOTO Statement](./GOTO Statement)。

### lower\_bound|upper\_bound

lower_bound和upper_bound可以为常量或变量，以默认递增模式说明如下：

*   当lower_bound<upper_bound时，从lower_bound开始循环赋值到counter，一直到upper_bound结束循环语句；
*   当lower_bound=upper_bound时，lower_bound赋值到counter，循环语句只会执行一次；
*   当lower_bound>upper_bound时，直接跳转到END LOOP结束循环语句。

***Example***

```plsql
<<G>>
DECLARE
i INT :=5;
BEGIN
  <<F1>>
  FOR i IN 1..9 LOOP
    IF i>5 THEN
      DBMS_OUTPUT.PUT_LINE(G.i);    -- ACCESS GLOBAL variable FROM the PROCEDURE body
      DBMS_OUTPUT.PUT_LINE(i || ':hello');
    ELSE
      FOR i IN 1..2 LOOP
        DBMS_OUTPUT.PUT_LINE(F1.i);   -- ACCESS LOCAL variable FROM the upper-LEVEL FOR statement
        DBMS_OUTPUT.PUT_LINE(i || ':world');
      END LOOP;
    END IF;
  END LOOP;
END;
/
 
--result
1
1:world
1
2:world
2
1:world
2
2:world
3
1:world
3
2:world
4
1:world
4
2:world
5
1:world
5
2:world
5
6:hello
5
7:hello
5
8:hello
5
9:hello


DECLARE
BEGIN
    FOR i IN 1..3 LOOP
        DBMS_OUTPUT.PUT_LINE('loop:'||i);
    END LOOP;
END;
/

--result
loop:1
loop:2
loop:3
```

for 隐式游标用法
-------
_for counter in (select xx from table) loop_

_......_

_end loop \[label_name\];_

其中， counter为隐式声明的局部变量，类型为其后的SELECT查询生成的默认RECORD类型，RECORD成员类型与SELECT查询列的类型一致，RECORD成员个数为查询列个数，不可被继承类型。

本用法为FOR循环支持隐式游标的用法，执行过程为每次循环时，FETCH查询语句的一行数据，将其填充到RECORD类型的counter，直到FETCH数据结束，循环也相应结束。
 
语句中使用了PL中变量等标识符在投影列时，需要指定别名用来后续访问。

为兼容语法，此处可以使用INTO子句，无实际作用。

***Example***

```sql
--SELECT查询
DECLARE
resultStr VARCHAR(200);
BEGIN
FOR file IN (SELECT area_no,area_name FROM area WHERE ROWNUM<5) LOOP
resultStr := resultStr||file.area_no||file.area_name;
DBMS_OUTPUT.PUT_LINE(resultStr);
END LOOP;
DBMS_OUTPUT.PUT_LINE(resultStr);
END;
/

--result
01EastChina
01EastChina02WestChina
01EastChina02WestChina03SouthChina
01EastChina02WestChina03SouthChina04NorthChina
01EastChina02WestChina03SouthChina04NorthChina
```

for 显式游标用法
-------
_for counter in cursor_x[()] loop_

_......_

_end loop \[label_name\];_

其中， cursor_x为一个已定义的显式游标，包括本地定义和全局定义显式游标，支持有参数和无参数。

此处的显式游标用法与正常使用显式游标一致，counter为隐式声明的局部变量，类型为其后的显式游标查询生成的默认RECORD类型，RECORD成员类型与显式查询列的类型一致，RECORD成员个数为查询列个数，不可被继承类型。

本用法为for循环支持显式游标的用法，执行过程为：在for循环的第一次隐式调用显式游标的open操作，之后每次循环隐式调用显式游标的fetch操作，循环结束时隐式调用显式游标的close操作。在for循环体中不允许显式调用作为索引的显式游标的open/fetch/close操作，否则均做报错处理。

***Example***

```plsql
-- 显式游标无参数
DECLARE
CURSOR c1 IS SELECT * FROM area ORDER BY area_no;
resultStr VARCHAR(200);
BEGIN
FOR v_sal IN c1 LOOP
resultStr := resultStr||v_sal.area_no||v_sal.area_name;
DBMS_OUTPUT.PUT_LINE(resultStr);
END LOOP;
END;
/

--result
01EastChina
01EastChina02WestChina
01EastChina02WestChina03SouthChina
01EastChina02WestChina03SouthChina04NorthChina
01EastChina02WestChina03SouthChina04NorthChina05CentralChina

-- 显式游标有参数
DECLARE
CURSOR c1(id INT) IS SELECT branch_no,branch_name FROM branches WHERE area_no = id ORDER BY branch_no;
resultStr VARCHAR(200);
BEGIN
FOR v_sal IN c1(1) LOOP
resultStr := resultStr||v_sal.branch_no||v_sal.branch_name;
DBMS_OUTPUT.PUT_LINE(resultStr);
END LOOP;
END;
/

--result
0101Shanghai
0101Shanghai0102Nanjing
0101Shanghai0102Nanjing0103Fuzhou
0101Shanghai0102Nanjing0103Fuzhou0104Xiamen
```

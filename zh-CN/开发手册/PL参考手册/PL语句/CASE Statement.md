CASE Statement为条件选择控制语句，包含如下两种形式：

*   CASE selector WHEN
*   CASE WHEN condition

CASE selector WHEN
------------------

格式为：

_CASE selector_  
_WHEN selector\_value\_1 THEN statements\_1_  
_WHEN selector\_value\_2 THEN statements\_2_  
_..._  
_WHEN selector\_value\_n THEN statements\_n_  
_\[ ELSE __else\_statements \]_  
_END CASE;_

其中，selector、selector\_value\_1 、selector\_value\_2、...、selector\_value\_n为表达式形式，selector不能为布尔型。

含义为：

首先计算CASE语句里的selector的值，如果值为NULL，则跳过所有WHEN语句向下寻找ELSE语句，如能找到ELSE语句，则执行else\_statements ，否则报错。

如果selector的值不为NULL，则将此值与向下第一个WHEN语句里的selector\_value\_1的值进行比较：

- 如果selector的值与selector_value_1的值相等，则执行第一个WHEN语句里THEN之后的statements_1，然后跳转到END CASE结束；

- 如果selector的值与selector_value_1的值不相等，则跳转到第二个WHEN语句，执行与第一个WHEN语句相同的操作；

- ......

- 如果selector的值与selector_value_n的值相等，则执行第n个WHEN语句里THEN之后的statements_n，然后跳转到END CASE结束；

- 如果selector的值与selector_value_n的值不相等，则跳转到下一条语句。

    - 如果下一条语句为ELSE语句，则执行else_statements，然后跳转到END CASE结束。

    - 如果下一条语句不为ELSE语句，则抛出错误。

示例

```plsql
CREATE OR REPLACE PROCEDURE ya_proc() IS
i INT:=0;
BEGIN
    CASE i
    WHEN 1 THEN
        DBMS_OUTPUT.PUT_LINE('hello');
    WHEN 2 THEN
        DBMS_OUTPUT.PUT_LINE('world');
    END CASE;
END ya_proc;
/
 
exec ya_proc;
 
--result
YAS-05210 case not found

-- 包含ELSE语句
CREATE OR REPLACE PROCEDURE ya_proc(a int) IS
BEGIN
    CASE a
    when 2 THEN
        DBMS_OUTPUT.PUT_LINE('loop2');
    when 3 THEN
        DBMS_OUTPUT.PUT_LINE('loop3');
    ELSE
        DBMS_OUTPUT.PUT_LINE('loop');
    END CASE;
END;
/

exec ya_proc(3);

--result
loop3
```

CASE WHEN condition
-------------------

格式为：

_CASE_  
_WHEN condition\_1 THEN statements\_1_  
_WHEN condition\_2 THEN statements\_2_  
_..._  
_WHEN condition\_n THEN statements\_n_  
_\[ ELSE __else\_statements \]_  
_END CASE;_

其中，condition\_1 、condition\_2、...、condition\_n为能产生布尔值结果的条件表达式。

含义为：

首先判断第一个WHEN语句里的condition\_1条件：

- 如果condition\_1的值为true，执行第一个WHEN语句里THEN之后的statemnets\_1，然后跳转到END CASE结束；

- 如果condition\_1的值为false，跳转到第二个WHEN语句，执行与第一个WHEN语句相同的操作；

- ......

- 如果condition\_n的值为true，执行第n个WHEN语句里THEN之后的statemnets\_n，然后跳转到END CASE结束；

- 如果condition\_n的值为false，则跳转到下一条语句。

    - 如果下一条语句为ELSE语句，则执行else\_statements，然后跳转到END CASE结束。

    - 如果下一条语句不为ELSE语句，则抛出错误。

示例

```plsql
CREATE OR REPLACE PROCEDURE ya_proc() IS
BEGIN
    CASE WHEN true THEN
        DBMS_OUTPUT.PUT_LINE('hello');
    WHEN false THEN
        DBMS_OUTPUT.PUT_LINE('world');
    END CASE;
END;
/
 
exec ya_proc;
 
--result
hello
```

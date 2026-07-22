IF Statement为条件选择控制语句，包含如下三种形式：

*   IF THEN
*   IF THEN ELSE 
*   IF THEN ELSIF

IF THEN
-------

格式为：

_IF condition THEN statements END IF;_

其中，condition为能产生布尔值结果的表达式。

含义为：

判断IF语句里的condition条件：

*   如果condition的值为true，执行THEN后面的Statements，然后继续执行到END IF结束；
*   如果condition的值为false，不执行任何操作，跳转到END IF结束。

示例

```plsql
DECLARE
i int;
BEGIN
    i := 0;
    IF i < 5 THEN
        i := i + 1;
        DBMS_OUTPUT.PUT_LINE ('This is: '||i);
    END IF;
END;
/
 
--result
This is: 1
```

IF THEN ELSE
------------

格式为：

_IF condition THEN statements_  
_ELSE statements_  
_END IF;_

其中，condition为能产生布尔值结果的表达式。

含义为：

首先判断IF语句里的condition条件：

*   如果condition的值为true，执行IF语句里THEN后面的Statements，然后跳转到END IF结束；
*   如果condition的值为false，跳转到ELSE语句，执行ELSE语句里的statements，然后继续执行到END IF结束。

示例

```plsql
DECLARE
i int;
BEGIN
    i := 5;
    IF i < 5 THEN
        i := i + 1;
        DBMS_OUTPUT.PUT_LINE ('This is: '||i);
    ELSE
        DBMS_OUTPUT.PUT_LINE ('This is: '|| (i-5));
    END IF;
END;
/
 
--result
This is: 0
```

IF THEN ELSIF
-------------

格式为：

_IF condition\_1 THEN statements\_1_  
_ELSIF condition\_2 THEN statements\_2_  
_......_

_ELSIF condition\_n THEN statements\_n_  
_\[ELSE else\_statements\]_  
_END IF;_

其中，condition\_1、_condition\_2 、...、condition\_n_ 为能产生布尔值结果的表达式。

含义为：

首先判断IF语句里的condition\_1条件：

*   如果condition\_1的值为true，执行IF语句里THEN后面的Statements\_1，然后跳转到END IF结束；
*   如果condition\_1的值为false，跳转到ELSIF，执行与IF语句相同的操作；
*   ......
*   如果condition\_n的值为true，执行ELSIF语句里THEN后面的Statements\_n，然后跳转到END IF结束；
*   如果condition\_n的值为false，跳转到下一条语句：

如果下一条语句为ELSE语句，则执行else\_statements，然后跳转到END IF结束。

如果下一条语句为END IF语句，则不执行任何操作结束。

示例

```plsql
DECLARE
i int;
BEGIN
    i := 5;
    IF i < 5 THEN
        i := i + 1;
        DBMS_OUTPUT.PUT_LINE ('This is: '||i);
    ELSIF i>5 THEN
        DBMS_OUTPUT.PUT_LINE ('This is: '|| (i-5));
    ELSE
        DBMS_OUTPUT.PUT_LINE ('This is: '|| (i+5));
    END IF;
END;
/
 
--result
This is: 10
```

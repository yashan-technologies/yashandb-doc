CASE 为条件选择控制操作符，包含如下两种形式：

*   CASE selector WHEN
*   CASE WHEN condition

CASE selector WHEN
------------------

格式为：

_CASE selector_  
_WHEN selector\_value\_1 THEN result\_1_  
_WHEN selector\_value\_2 THEN result\_2_  
_..._  
_WHEN selector\_value\_n THEN result\_n_  
_\[ ELSE __else\_result \]_  
_END;_

其中，selector、selector\_value\_1 、selector\_value\_2、...、selector\_value\_n为表达式形式。

含义为：

首先计算CASE语句里的selector的值，如果值为NULL，则跳过所有WHEN语句向下寻找ELSE语句，如能找到ELSE语句，则执行else\_result ，否则报错。

如果selector的值不为NULL，则将此值与向下第一个WHEN语句里的selector\_value\_1的值进行比较：

*   如果selector的值与selector\_value\_1的值相等，则执行第一个WHEN语句里THEN之后的result\_1，然后跳转到END结束；
*   如果selector的值与selector\_value\_1的值不相等，则跳转到第二个WHEN语句，执行与第一个WHEN语句相同的操作；
*   ......
*   如果selector的值与selector\_value\_n的值相等，则执行第n个WHEN语句里THEN之后的result\_n，然后跳转到END结束；
*   如果selector的值与selector\_value\_n的值不相等，则跳转到下一条语句：

如果下一条语句为ELSE语句，则执行else\_result，然后跳转到END结束。

示例（单机HEAP表）

```sql
SELECT CASE 1 WHEN 1 THEN 'one' WHEN 2 THEN 'two' ELSE 'more' END result;
result
------
one
```

CASE WHEN condition
-------------------

格式为：

_CASE_  
_WHEN condition\_1 THEN result\_1_  
_WHEN condition\_2 THEN result\_2_  
_..._  
_WHEN condition\_n THEN result\_n_  
_\[ ELSE __else\_result \]_  
_END;_

其中，condition\_1 、condition\_2、...、condition\_n为能产生布尔值结果的条件表达式。

含义为：

首先判断第一个WHEN语句里的condition\_1条件：

*   如果condition\_1的值为true，执行第一个WHEN语句里THEN之后的result\_1，然后跳转到END结束；
*   如果condition\_1的值为false，跳转到第二个WHEN语句，执行与第一个WHEN语句相同的操作；
*   ......
*   如果condition\_n的值为true，执行第n个WHEN语句里THEN之后的result\_n，然后跳转到END结束；
*   如果condition\_n的值为false，则跳转到下一条语句。

如果下一条语句为ELSE语句，则执行else\_result，然后跳转到END结束。

示例（单机HEAP表）

```sql
SELECT CASE WHEN 1 > 0 THEN 'true' ELSE 'false' END result;
result
------
true
```

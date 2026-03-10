```ebnf+diagram
wm_concat::= WM_CONCAT "(" [DISTINCT|ALL] string ")"
```

WM_CONCAT函数将多行的数据执行拼接操作，并通过分隔符（`,`）分隔，返回一行CLOB类型的字符串。

本函数与[GROUP_CONCAT](./GROUP_CONCAT)函数实现功能类似，区别在于WM_CONCAT不能自定义指定SEPARATOR分隔符，且不能指定ORDER BY排序。

**DISTINCT**

计算最终拼接结果时，过滤在同一组内出现的重复的行。

**ALL**

默认值，表示不过滤重复的行，对所有行都进行拼接。

**string**

string可以为通用表达式[expr](../通用SQL语法/expr)或查询列为单列且返回行为单行的子查询，string的值必须为CHAR、VARCHAR或除JSON、NCLOB和时区类型外可转换为CHAR或VARCHAR的其他类型。

- 在向量化执行引擎中，string不能为LOB类型的行外存储数据。

- 在单行计算中，当string的值为NULL时，函数返回NULL。

- 在多行计算中，函数将忽略string值为空的行，当所有行均为空时，计算结果为NULL。存在多个拼接行时，会将多行的结果使用`,`进行分隔。

示例

```sql
-- 创建exprs_wmconcat表，并插入数据
CREATE TABLE exprs_wmconcat (id INT,name VARCHAR(50),money FLOAT);
INSERT INTO exprs_wmconcat 
VALUES  (1,'小东',10000),(2,'小明',46450),
        (3,'小红',46450 ),(4,'小东',14465),
        (5,'小明',46450),(6,'小东',46450);

-- 未指定GROUP BY时，将所有行CONCAT，得到一行结果
SELECT WM_CONCAT(MONEY) AS money FROM exprs_wmconcat;
MONEY
----------------------------------------------------------------
1.0E+004,4.645E+004,4.645E+004,1.4465E+004,4.645E+004,4.645E+004

-- group by后各组的多行数据分别CONCAT成一行，得到按组的多行结果
SELECT NAME,WM_CONCAT(MONEY) AS money FROM exprs_wmconcat GROUP BY name;
NAME                 MONEY
-------------------- ----------------------------------------------
小东               1.0E+004,1.4465E+004,4.645E+004
小明               4.645E+004,4.645E+004
小红               4.645E+004

-- 使用DISTINCT关键字去重
SELECT WM_CONCAT(DISTINCT name) AS names FROM exprs_wmconcat;
NAMES
------------------------
小东,小明,小红
```
```ebnf
wm_concat = WM_CONCAT "(" [DISTINCT|UNIQUE|ALL] string ")" [ OVER "(" [query_partition_clause] [order_by_clause] ")" ]. 
```

WM\_CONCAT函数将多行的数据执行拼接操作，并通过分隔符（`,`）分隔，返回一行CLOB类型的字符串。

本函数与[GROUP_CONCAT](./GROUP_CONCAT)函数实现功能类似，区别在于WM\_CONCAT不能自定义指定SEPARATOR分隔符，且不能指定ORDER BY排序。

**DISTINCT|UNIQUE**

计算最终拼接结果时，过滤在同一组内出现的重复的行。

DISTINCT与UNIQUE完全等价。

目前，YashanDB无法对LOB类型的数据进行去重。

**ALL**

默认值，表示不过滤重复的行，对所有行都进行拼接。

**string**

string可以为通用表达式[expr](../通用SQL语法/expr)或查询列为单列且返回行为单行的子查询，string的值必须为字符型或除JSON时区类型外可转换为字符型的其他类型。

- 在向量化执行引擎中，string不能为LOB类型的行外存储数据。

- 在单行计算中，当string的值为NULL时，函数返回NULL。

- 在多行计算中，函数将忽略string值为空的行，当所有行均为空时，计算结果为NULL。存在多个拼接行时，会将多行的结果使用`,`进行分隔。

示例

```sql
-- 创建exprs_wmconcat表，并插入数据
CREATE TABLE exprs_wmconcat (id INT,name VARCHAR(50),money FLOAT);
INSERT INTO exprs_wmconcat 
VALUES  (1,'Dong',10000),(2,'Ming',46450),
        (3,'Hong',46450 ),(4,'Dong',14465),
        (5,'Ming',46450),(6,'Dong',46450);

-- 未指定GROUP BY时，将所有行CONCAT，得到一行结果
SELECT WM_CONCAT(MONEY) AS money FROM exprs_wmconcat;
MONEY
----------------------------------------------------------------
1.0E+004,4.645E+004,4.645E+004,1.4465E+004,4.645E+004,4.645E+004

-- group by后各组的多行数据分别CONCAT成一行，得到按组的多行结果
SELECT NAME,WM_CONCAT(MONEY) AS money FROM exprs_wmconcat GROUP BY name;
NAME                 MONEY
-------------------- ----------------------------------------------
Dong               1.0E+004,1.4465E+004,4.645E+004
Ming               4.645E+004,4.645E+004
Hong               4.645E+004

-- 使用DISTINCT关键字去重
SELECT WM_CONCAT(DISTINCT name) AS names FROM exprs_wmconcat;
NAMES
------------------------
Dong,Ming,Hong
```

**OVER**

当指定OVER关键字时，WM_CONCAT将作为[窗口函数](00内置函数.md#WindowFunction)，返回多行的数值和，返回类型为CLOB。

WM_CONCAT作为窗口函数时，string不能为时间类型。

**query\_partition\_clause**

窗口函数通用语法。

**order\_by\_clause**

窗口函数通用语法。

示例

```sql
CREATE TABLE tb_wm_concat_fruit(id INT,name VARCHAR(100), num INT);
INSERT INTO tb_wm_concat_fruit VALUES(1,'apple', 11);
INSERT INTO tb_wm_concat_fruit VALUES(2,'orange', 22);
INSERT INTO tb_wm_concat_fruit VALUES(3,'banana', 33);
INSERT INTO tb_wm_concat_fruit VALUES(3,'banana', 44);
INSERT INTO tb_wm_concat_fruit VALUES(3,'banana', 55);
INSERT INTO tb_wm_concat_fruit VALUES(4,'cream', 66);
INSERT INTO tb_wm_concat_fruit VALUES(4,'cream', 77);
COMMIT;

SQL> SELECT id,WM_CONCAT(name) OVER (PARTITION BY id) AS fruit_name FROM tb_wm_concat_fruit;

          ID FRUIT_NAME
------------ ----------------------------------------------------------------
           1 apple
           2 orange
           3 banana,banana,banana
           3 banana,banana,banana
           3 banana,banana,banana
           4 cream,cream
           4 cream,cream

7 rows fetched.

SQL> SELECT id,WM_CONCAT(DISTINCT name) OVER (PARTITION BY id) AS fruit_name FROM tb_wm_concat_fruit;

          ID FRUIT_NAME
------------ ----------------------------------------------------------------
           1 apple
           2 orange
           3 banana
           3 banana
           3 banana
           4 cream
           4 cream

7 rows fetched.

SQL> SELECT id,WM_CONCAT(name) OVER (PARTITION BY id ORDER BY num) AS fruit_name FROM tb_wm_concat_fruit;

          ID FRUIT_NAME
------------ ----------------------------------------------------------------
           1 apple
           2 orange
           3 banana
           3 banana,banana
           3 banana,banana,banana
           4 cream
           4 cream,cream

7 rows fetched.
```

```ebnf+diagram
nullif::= NULLIF "(" expr1 "," expr2 ")"
```

NULLIF函数用于比较[expr1](../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)和[expr2](../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)的值，相等时返回NULL，不相等时返回expr1的值。

本函数遵循如下规则：

- expr1和expr2均不能为LOB类型、JSON类型。

- CHAR、VARCHAR与NCHAR、NVARCHAR可以混合运算。

- 若expr1和expr2的数据类型不同且无法按照一定的规则进行转换，函数将返回类型转换错误。

- expr1、expr2其中之一为数值类型，另一个为数值的字符串类型时，函数返回类型转换错误。

- 当expr1的计算结果值为NULL时，函数返回NULL。当expr1为NULL常量时，函数将报错。

- 当expr2为NULL时，函数返回expr1的值。

函数返回值类型规则如下：

- expr1和expr2均为数值型时，函数将会确定其中具有最高精度的数据类型，并将计算结果转为该类型返回。

- expr1和expr2均为字符型时，函数返回值类型为expr1的类型。

- expr1和expr2为DATE、TIMESTAMP、时区类型时，返回类型优先级为TIMESTAMP TZ -> TIMESTAMP LTZ -> TIMESTAMP -> DATE。

- 除上述场景外，当expr1与expr2的类型相同时函数返回此数据类型的值。

示例

```sql
--expr1和expr2的值不相等时
SELECT NULLIF('123','456') res FROM DUAL;
RES
----- 
123

--expr1和expr2的值相等时
SELECT NULLIF('123','123') res FROM DUAL;
RES
-----

--sales_info表包含了机构的11001产品的销售数量和金额信息，现对其增加一条数量和金额均为0的记录
INSERT INTO sales_info VALUES ('2021','06','0101','11001',0,0,'');
--当需要计算11001产品的在每个月的销售单价时，会出现除以0错误
SELECT year,month,product,amount/quantity 
FROM sales_info
WHERE product='11001';
[1:33]YAS-00011 divided by zero
--使用NULLIF函数提前预防此类错误，减少异常捕获处理，避免应用在此中断
SELECT year,month,product,amount/NULLIF(quantity,0) price
FROM sales_info
WHERE product='11001';
YEAR  MONTH PRODUCT         PRICE
----- ----- --------- -----------
2021  10    11001              15
2001  01    11001      16.6666667
2000  12    11001              15
2015  03    11001              15
2021  05    11001              15
2021  06    11001
2015  11    11001              15
```

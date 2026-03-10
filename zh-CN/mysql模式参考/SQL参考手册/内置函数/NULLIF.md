```ebnf+diagram
nullif::= NULLIF "(" expr1 "," expr2 ")"
```

NULLIF函数用于比较[expr1](../通用SQL语法/expr)和[expr2](../通用SQL语法/expr)的值，相等时返回NULL，不相等时返回expr1的值。

本函数遵循如下规则：

- expr1和expr2均不能为BLOB类型、TEXT类型。

- CHAR、VARCHAR与NCHAR、NVARCHAR可以混合运算。

- 若expr1和expr2的数据类型不同且无法按照一定的规则进行转换，函数将返回类型转换错误。

- expr1、expr2其中之一为数值类型，另一个为数值的字符串类型时，函数返回类型转换错误。

- 当expr1的计算结果值为NULL时，函数返回NULL。当expr1为NULL常量时，函数将报错。

- 当expr2为NULL时，函数返回expr1的值。

函数返回值类型规则如下：

- expr1和expr2均为数值型时，函数将会确定其中具有最高精度的数据类型，并将计算结果转为该类型返回。

- expr1和expr2均为字符型时，函数返回值类型为expr1的类型。

- expr1、expr2其中之一为DATE类型，另一个为TIMESTAMP类型时，函数返回TIMESTAMP类型。

- 除上述场景外，当expr1与expr2的类型相同时函数返回此数据类型的值。

示例（单机HEAP表）

```sql
--expr1和expr2的值不相等时
SELECT NULLIF('123','456') res FROM DUAL;
res
----- 
123

--expr1和expr2的值相等时
SELECT NULLIF('123','123') res FROM DUAL;
res
-----

--sales_info1表包含了机构的11001产品的销售数量和金额信息，其中存在1条数量和金额均为0的记录
CREATE TABLE sales_info1
(year CHAR(4) NOT NULL,
 month CHAR(2) NOT NULL,
 branch CHAR(4) NOT NULL,
 product CHAR(5) NOT NULL,
 quantity NUMBER DEFAULT 0 NOT NULL,
 amount NUMBER(10,2) DEFAULT 0 NOT NULL,
 salsperson CHAR(10) NOT NULL)
PARTITION BY HASH(branch);
INSERT INTO sales_info1 VALUES ('2001','01','0201','11001',30,500,'Anna');
INSERT INTO sales_info1 VALUES ('2021','06','0101','11001',0,0,'Mask');

--当需要计算11001产品的在每个月的销售单价时，会出现除以0错误
SELECT year,month,product,amount/quantity 
FROM sales_info1
WHERE product='11001';
[1:33]YAS-00011 divided by zero

--使用NULLIF函数提前预防此类错误，减少异常捕获处理，避免应用在此中断
SELECT year,month,product,amount/NULLIF(quantity,0) price
FROM sales_info1
WHERE product='11001';
year  month product         price
----- ----- --------- -----------
2021  06    11001
2015  01    11001      16.6666667
```

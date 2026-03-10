```ebnf+diagram
bin_to_num::= BIN_TO_NUM "(" [(expr) {"," (expr)}] ")"
```

BIN_TO_NUM函数用于实现二进制向十进制的转换，将[expr](../通用SQL语法/expr)组合表示的二进制位向量串转换成与其等效的数值，返回一个NUMBER类型的数据。

BIN_TO_NUM函数仅适用于HEAP表。

本函数支持[0,65535]个expr作为参数，无入参时直接返回0，有入参时返回转换结果，但若计算的数值超过NUMBER类型可表示的范围则报错。

每一个expr表示位向量中的一个位，其值可以为任何数值型或可隐式转换为NUMBER类型的字符型数据。

所有expr的值都必须为0/1，或进行取整后为0/1，否则报错。取整规则为：

- 如果expr为FLOAT/DOUBLE类型，函数对其进行奇进偶舍取整（例如：1.5->2，0.5->0）。
- 对于其他类型，函数进行去掉小数位取整（例如：1.3->1，1.6->1）。

示例（HEAP表）

```sql
-- 将二进制位向量串11转换为十进制3
SELECT BIN_TO_NUM(1,1) res FROM DUAL;
RES
-----------
3

-- 将二进制位向量串1101转换为十进制13
SELECT BIN_TO_NUM(b'1',1,0,1) res FROM DUAL;
RES
-----------
13

--创建bt_table_bin_to_num表，包含多个浮点型字段
CREATE TABLE bt_table_bin_to_num(c1 DOUBLE, c2 DOUBLE, c3 DOUBLE, c4 DOUBLE);
INSERT INTO bt_table_bin_to_num VALUES(0.3,0.8,1.3,1.8);

-- 对于NUMBER数据，函数按去小数位取整后进行十进制转换
-- 对于浮点型小数，函数按四舍五入取整后进行十进制转换
SELECT BIN_TO_NUM(0.3) "0.3", BIN_TO_NUM(c1) "d0.3", 
BIN_TO_NUM(0.8) "0.8", BIN_TO_NUM(c2) "d0.8", 
BIN_TO_NUM(1.3) "1.3", BIN_TO_NUM(c3) "d1.3"
FROM bt_table_bin_to_num;
        0.3        d0.3         0.8        d0.8         1.3        d1.3 
----------- ----------- ----------- ----------- ----------- ----------- 
          0           0           0           1           1           1

--对于NUMBER数据，函数按去小数位取整为1后进行十进制转换
SELECT BIN_TO_NUM(1.8) res FROM DUAL;
RES
-----------
1

--c4字段按四舍五入取整为2后进行十进制转换，导致报错
SELECT BIN_TO_NUM(c4) res FROM bt_table_bin_to_num;
[1:19]YAS-06001 the value of parameter expression is invalid, the value of the expression must be 0 or 1
```

```ebnf+diagram
mod::= MOD "(" expr1 "," expr2 ")"
```
MOD为取模函数，与[算术运算符](../运算符/算术运算符)算法一致。

入参类型与返回类型映射关系如下：

| expr1                         | expr2             | 返回值     |
|-------------------------------|-------------------|---------|
| 字符型、DOUBLE、FLOAT、BINARY             | 任意                | DOUBLE  |
| 任意                            | 字符型、DOUBLE、FLOAT、BINARY | DOUBLE  |
| NUMBER、日期型                    | 任意                | NUMBER  |
| 任意                            | NUMBER、日期型        | NUMBER  |
| BOOL、TINYINT、SMALLINT、INTEGER | 任意                | INTEGER |
| BIGINT、BIT                    | 任意                | BIGINT  |

**expr1、expr2**

[通用表达式](../通用SQL语法/expr)，其值的数据类型如前文表中的入参类型所示。

若同时满足不同行的入参规则，则匹配规则为由上到下优先匹配。如expr1为bit类型，expr2为char类型，将返回double类型。

- 当expr1或expr2的值为日期类型时，将日期类型转换为具体的年月日数值后进行取模计算。

- 当expr1或expr2中任一值为NULL时，函数返回NULL。

示例（单机HEAP表）

```sql
SELECT MOD(7,2) mod1,
MOD(10.97,1.23) mod2,
MOD('4.55',2) mod3
FROM DUAL;
mod1        mod2        mod3 
------------ ----------- ----------- 
           1        1.13    5.5E-001
```

```ebnf
truncate = TRUNCATE "(" expr "," n ")".
```

TRUNCATE函数将[expr](../通用SQL语法/expr.md)的值截断至n位小数，直接舍弃多余位，不进行四舍五入。

**expr**

通用表达式，其值的数据类型要求以及对应的返回值类型映射关系如下：

|  expr的类型| 返回类型|
|------------------|--------|
| TINYINT          | INT    |
| SMALLINT         | INT    |
| INT              | INT    |
| BOOL             | INT    |
| BIT              | BIGINT    |
| BIGINT           | BIGINT |
| TINYINT UNSIGNED | BIGINT UNSIGNED    |
| SMALLINT UNSIGNED        | BIGINT UNSIGNED    |
| MEDIUMINT UNSIGNED       | BIGINT UNSIGNED    |
| INT UNSIGNED             | BIGINT UNSIGNED    |
| BIGINT UNSIGNED          | BIGINT UNSIGNED |
| FLOAT            | DOUBLE |
| DOUBLE           | DOUBLE |
| CHAR             | DOUBLE |
| NCHAR            | DOUBLE |
| VARCHAR          | DOUBLE |
| NVARCHAR         | DOUBLE |
| DATE             | DOUBLE |
| TIME             | DOUBLE |
| TIMESTAMP        | DOUBLE |
| NUMBER           | NUMBER |

- 当用于截断数值类型时，expr的值必须为数值型或可以转换为NUMBER类型的其他类型（转换失败返回Invalid number错误）。

- 当expr的值为日期类型时，将日期类型转换为具体的年月日数值后进行截断处理。

- 当expr的值为NULL时，函数返回NULL。

**n**

用于指定截断的位置，必须为数值型或可以转换为数值的其他类型。



具体的截断规则如下：

|  n值| 函数表现| 示例|
| --- | --- | --- |
| n为小数  | 以n的整数部分作为位数进行数值截断 | TRUNCATE(123.456, 1.6) = TRUNCATE(123.456, 1) <br/> TRUNCATE(123.456, -1.2) = TRUNCATE(123.456, -1) |
| n = 0   | 截断小数部分 | TRUNCATE(123.456, 0) = 123 |
| n > 0 且未超出number_expr小数位数 | 截断该小数位数往后的部分 | TRUNCATE(123.456, 2) = 123.45 |
| n > 0 且超出number_expr小数位数 | 原数据返回 | TRUNCATE(123.456, 5) = 123.456 |
| n < 0 且未超出number_expr整数部分位数 | 截断小数部分，并将整数部分从右至左的&#124;n&#124;位数置为0 | TRUNCATE(123.456, -1) = 120 <br/> TRUNCATE(123.456, -2) = 100 |
| n < 0 且超出number_expr整数部分位数 | 返回0 | TRUNCATE(123.456, -5) = 0 |
| n为NULL | 返回NULL |     |



示例（HEAP表）

```sql
SELECT TRUNCATE(1.123, 1) res FROM DUAL;
res                   
--------------------- 
1.1

SELECT TRUNCATE(18.123, -1) res FROM DUAL;
res
--------------------- 
10
    
SELECT TRUNCATE(null, 1) res FROM DUAL;
res       
--------- 
NULL 
```

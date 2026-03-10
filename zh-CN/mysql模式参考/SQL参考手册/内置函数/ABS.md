```ebnf+diagram
abs::= ABS "(" expr ")"
```
ABS函数计算[expr](../通用SQL语法/expr)的绝对值。

入参类型与返回类型映射关系如下：

|  参数类型| 返回值|
|------------------|--------|
| TINYINT          | INT    |
| SMALLINT         | INT    |
| INT              | INT    |
| BOOL             | INT    |
| BIGINT           | BIGINT |
| TINYINT UNSIGNED | INT UNSIGNED   |
| SMALLINT UNSIGNED        |  INT UNSIGNED      |
| MEDIUMINT UNSIGNED       |  BIGINT UNSIGNED     |
| INT UNSIGNED             |  BIGINT UNSIGNED     |
| BIGINT UNSIGNED          |  BIGINT UNSIGNED     |
| BIT              | BIGINT |
| NUMBER           | NUMBER |
| FLOAT            | DOUBLE |
| DOUBLE           | DOUBLE |
| CHAR             | DOUBLE |
| VARCHAR          | DOUBLE |
| NCHAR            | DOUBLE |
| NVARCHAR         | DOUBLE |
| DATE             | DOUBLE |
| TIME             | DOUBLE |
| TIMESTAMP        | DOUBLE |
| BINARY           | DOUBLE |

**expr**

[通用表达式](../通用SQL语法/expr)，其值的数据类型如前文表中的入参类型所示。

- 当expr的值为日期类型时，将日期类型转换为具体的年月日数值后进行绝对值计算。

- 当expr的值为NULL时，函数返回NULL。

- 不允许对BLOB和TEXT类型数据使用此函数。

示例（单机HEAP表）

```sql
SELECT ABS(-2.345) abs1, 
ABS(1/10) abs2, 
ABS(1-2*3) abs3, 
ABS(TO_NUMBER('$3.33','$9.99')) abs4 
FROM DUAL;
 
       abs1        abs2                  abs3        abs4
----------- ----------- --------------------- -----------
      2.345          .1                     5        3.33
```

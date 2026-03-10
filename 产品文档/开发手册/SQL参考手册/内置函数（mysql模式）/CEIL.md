```ebnf+diagram
ceil::= CEIL "(" expr ")"
```
CEIL函数对[expr](../通用SQL语法/expr)向上取整。

入参类型与返回类型映射关系如下：

| 参数类型      | 返回值 |
|-----------|-----|
| TINYINT   | INT |
| SMALLINT  | INT |
| INT       | INT |
| BOOL      | INT |
| BIT       | BIGINT |
| NUMBER    | BIGINT |
| BIGINT    | NUMBER |
| TINYINT UNSIGNED | BIGINT UNSIGNED   |
| SMALLINT UNSIGNED        |  BIGINT UNSIGNED      |
| MEDIUMINT UNSIGNED        |  BIGINT UNSIGNED      |
| INT UNSIGNED             |  BIGINT UNSIGNED     |
| BIGINT UNSIGNED          |  BIGINT UNSIGNED     |
| FLOAT     | DOUBLE |
| DOUBLE    | DOUBLE |
| CHAR      | DOUBLE |
| VARCHAR   | DOUBLE |
| NCHAR     | DOUBLE |
| NVARCHAR  | DOUBLE |
| DATE      | DOUBLE |
| TIME      | DOUBLE |
| TIMESTAMP | DOUBLE |
| BINARY | DOUBLE |

**expr**

[通用表达式](../通用SQL语法/expr)，其值的数据类型如前文表中的入参类型所示。

- 当expr的值为日期类型时，将日期类型转换为具体的年月日数值后进行向上取整计算。

- 当expr的值为NULL时，函数返回NULL。

示例（单机HEAP表）

```sql
SELECT CEIL(7) ceil1,
CEIL('0.97') ceil2,
CEIL(6.22) ceil3
FROM DUAL;
       CEIL1       CEIL2       CEIL3
------------ ----------- -----------
           7    1.0E+000           7
```
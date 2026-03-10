```ebnf+diagram
round::= ROUND "("expr ["," round_number]")"
```

ROUND函数对[expr](../通用SQL语法/expr)按照round_number指定的位数进行四舍五入，返回计算结果。

入参类型与返回类型映射关系如下：

| 入参类型             | 返回类型   |
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
| BIGINT UNSIGNED            | BIGINT UNSIGNED    |
| FLOAT            | DOUBLE |
| DOUBLE           | DOUBLE |
| CHAR             | DOUBLE |
| NCHAR            | DOUBLE |
| VARCHAR          | DOUBLE |
| NVARCHAR         | DOUBLE |
| DATE             | DOUBLE |
| TIME             | DOUBLE |
| TIMESTAMP        | DOUBLE |
| BINARY           | DOUBLE |
| NUMBER           | NUMBER |

**expr**

[通用表达式](../通用SQL语法/expr)，其值的数据类型如前文表中的入参类型所示。

- 当expr的值为日期类型时，将日期类型转换为具体的年月日数值后进行指定位数的四舍五入计算。

- 当expr或round_number中任一值为NULL时，函数返回NULL。

示例（单机HEAP表）

```sql
SELECT ROUND(7.11) round1,
       ROUND(10.97) round2,
       ROUND(4.55,5) round3
FROM DUAL;
round1      round2      round3 
----------- ----------- ----------- 
          7          11        4.55
```


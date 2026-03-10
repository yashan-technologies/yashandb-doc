```ebnf+diagram
abs::= ABS "(" expr ")"
```

ABS函数计算一个数值的绝对值，其返回值的数据类型为：

|  参数类型| 返回值|
| --- | --- |
| TINYINT | SMALLINT |
| SMALLINT | INT |
| INT | BIGINT |
| BIGINT | BIGINT/NUMBER |
| NUMBER | NUMBER |
| FLOAT | FLOAT |
| DOUBLE | NUMBER |
| CHAR | NUMBER |
| VARCHAR | NUMBER |
| NCHAR | NUMBER |
| NVARCHAR | NUMBER |

其中[expr](../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)的值为数值型或可以转换为NUMBER类型的字符型（转换失败返回Invalid number错误）。对于其他类型，函数返回类型不支持。

当expr的值为NULL时，函数返回NULL。

示例

```sql
SELECT ABS(-2.345) abs1, 
ABS(1/10) abs2, 
ABS(1-2*3) abs3, 
ABS(TO_NUMBER('$3.33','$9.99')) abs4 
FROM DUAL;
 
       ABS1        ABS2                  ABS3        ABS4
----------- ----------- --------------------- -----------
      2.345          .1                     5        3.33
```

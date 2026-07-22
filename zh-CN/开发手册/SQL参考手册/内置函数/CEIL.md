```ebnf
ceil = CEIL "(" expr ")".
```

CEIL函数对[expr](../通用SQL语法/expr)表示的数据进行向上取整，其返回规则为：

*   当expr的值为数值型时，返回与其相同数据类型的数据。
*   当expr的值为字符型时，返回NUMBER类型的数据。
*   当expr的值为NULL时，返回NULL。
*   当expr的值为浮点类型特殊值时：

    *   Nan：函数返回Nan
    *   Inf：函数返回Inf
    *   \-Inf：函数返回-Inf

其中expr的值为数值型或可以转换为NUMBER类型的字符型（转换失败返回Invalid number错误）。对于其他类型，函数返回类型不支持。

示例

```sql
SELECT CEIL(7) ceil1,
CEIL('0.97') ceil2,
CEIL(6.22) ceil3
FROM DUAL;
       CEIL1       CEIL2       CEIL3
------------ ----------- -----------
           7           1           7
 
--创建number_fd表，包含FLOAT、DOUBLE字段
CREATE TABLE number_fd1(numberf FLOAT, numberd DOUBLE);
INSERT INTO number_fd1 VALUES('Nan','Nan');
INSERT INTO number_fd1 VALUES('Inf','Inf');
INSERT INTO number_fd1 VALUES('-inf','-inf');
COMMIT;
 
SELECT CEIL(numberf) ceilf,
CEIL(numberd) ceild
FROM number_fd1;
      CEILF       CEILD
----------- -----------
        Nan         Nan
        Inf         Inf
       -Inf        -Inf
```

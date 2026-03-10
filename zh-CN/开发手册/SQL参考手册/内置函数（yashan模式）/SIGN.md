```ebnf+diagram
sign::= SIGN "(" expr ")"
```

SIGN函数返回[expr](../通用SQL语法/expr)表示的数值的符号，包括1、-1、0。

expr的值须为数值型或可以转换为NUMBER类型的字符型（转换失败返回Invalid number错误）。对于其他类型，函数返回类型不支持。

当expr的值为NULL时，函数返回NULL。 

当expr的值为正数时，函数返回1；当expr的值为负数时，函数返回-1；当expr的值为0时，函数返回0。

对于浮点数据里的Nan、-Inf、Inf特殊值：

- SIGN(Nan) = 1
- SIGN(Inf) = 1
- SIGN(-Inf) = -1

示例

```sql
DROP TABLE IF EXISTS number_fd;
CREATE TABLE number_fd(numberf FLOAT, numberd DOUBLE);
INSERT INTO number_fd VALUES('NaN','inf');
INSERT INTO number_fd VALUES('',5.5333333323);
INSERT INTO number_fd VALUES('-inf','-4.322323');
COMMIT;
 
SELECT SIGN(numberf) sign1, SIGN(numberd) sign2 FROM number_fd;
      SIGN1       SIGN2
----------- -----------
          1           1
                      1
         -1          -1
```

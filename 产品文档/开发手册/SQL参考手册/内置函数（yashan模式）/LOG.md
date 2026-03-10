```ebnf+diagram
log::= LOG "(" expr1 "," expr2 ")"
```

LOG函数计算expr2以expr1为底的对数，返回一个DOUBLE类型的数值。

expr1和expr2均为YashanDB认可的[通用表达式](../通用SQL语法/expr)，其值须为数值型或可以转换为NUMBER类型的字符型（转换失败返回Invalid number错误）。对于其他类型，函数返回类型不支持。

当expr1或者expr2的值为NULL时，函数返回NULL。

根据对数的数学概念，expr1应该为除0和1以外的正数，expr2应该为任意一个正数，除此之外的其他情况函数处理规则见下表：

| expr2值\expr1值 | 非0/1正数 | -Inf | 负数 |  0   |  1   | Inf  | Nan  | -Nan |
| :-------------: | :-------: | :--: | :--: | :--: | :--: | :--: | :--: | ---- |
|    **正数**     |   对数    | Nan  | Nan  |  0   | Inf  |  0   | Nan  | Nan  |
|    **-Inf**     |    Nan    | Nan  | Nan  | Nan  | Nan  | Nan  | Nan  | Nan  |
|    **负数**     |    Nan    | Nan  | Nan  | Nan  | Nan  | Nan  | Nan  | Nan  |
|      **0**      |   -Inf    | Nan  | Nan  | Nan  | -Inf | Nan  | Nan  | Nan  |
|     **Inf**     |    Inf    | Nan  | Nan  | Nan  | Inf  | Nan  | Nan  | Nan  |
|     **Nan**     |    Nan    | Nan  | Nan  | Nan  | Nan  | Nan  | Nan  | Nan  |
|    **-Nan**     |    Nan    | Nan  | Nan  | Nan  | Nan  | Nan  | Nan  | Nan  |

示例

```sql
SELECT LOG(2,4) res FROM DUAL;
RES
-----------
2.0E+000

SELECT LOG(b'10',b'100') res FROM DUAL;
RES
-----------
2.0E+000

DROP TABLE IF EXISTS number_fd;
CREATE TABLE number_fd(numberf FLOAT, numberd DOUBLE);
INSERT INTO number_fd VALUES(0,5.55);
INSERT INTO number_fd VALUES(1,5.55);
INSERT INTO number_fd VALUES(2,-5.55);
INSERT INTO number_fd VALUES(2,0);
INSERT INTO number_fd VALUES('2','Inf');
INSERT INTO number_fd VALUES('2','-Inf');
INSERT INTO number_fd VALUES('2','Nan');
INSERT INTO number_fd VALUES('2','-Nan');

SELECT LOG(numberf,numberd) res1, LOG(numberd,numberf) res2 FROM number_fd;
       RES1        RES2 
----------- ----------- 
          0        -Inf
        Inf           0
        Nan         Nan
       -Inf           0
        Inf           0
        Nan         Nan
        Nan         Nan
        Nan         Nan
```
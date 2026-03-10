```ebnf+diagram
addtime::= ADDTIME "(" expr1 "," expr2 ")" 
```

ADDTIME函数将expr2加到expr1中并返回结果。expr1是time或datetime表达式，expr2是time表达式。

**expr1**

- expr1为YashanDB认可的[通用表达式](../通用SQL语法/expr)，必须可转换为TIME类型或者DATETIME类型。

- 若expr1不是时间日期类型且不是数值类型时，会将expr1先转为字符串类型。根据转换后的字符串如果存在空格将其分为两段有效字符串的情况，则转成DATETIME类型，否则尝试转为TIME类型，如失败则转换成DATETIME类型。

**expr2**

- expr2为YashanDB认可的[通用表达式](../通用SQL语法/expr)，必须可转换为TIME类型。

- 若expr2为DATETIME类型，计算时取TIME部分参与计算。

- 若expr2不是时间日期类型且不是数值类型时，会将expr2先转为字符串类型，然后尝试转为TIME类型，如失败则转换成DATETIME类型。

示例（HEAP表）

```sql
SELECT ADDTIME('2021-02-03', '11:11:11') res FROM dual;

res
---------------------------------
2021-02-03 11:11:11

SELECT ADDTIME("1997-12-31 23.59.59.999999", "1998-01-01  01:01:01.99999") res;

res
---------------------------------
1998-01-01 01:01:01.999989

SELECT ADDTIME("1997-12-31 23.59.59.999999", "01:01:01.99999") res;

res
---------------------------------
1998-01-01 01:01:01.999989
```

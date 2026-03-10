```ebnf+diagram
concat::= CONCAT "(" expr "," (expr) {"," (expr)} ")"
```

CONCAT函数将多个[expr](../通用SQL语法/expr)的值连接成一个字符串，等同于`expr||expr||……`。

本函数遵循如下规则：

- 本函数的参数须为[1,65535]个。

- 如果参数中包含二进制类型或bit类型，则函数返回类型为二进制类型，否则函数返回类型为字符类型。

- 如果任何参数为NULL，则函数返回结果为NULL。

示例（单机HEAP表）

```sql
SELECT CONCAT('a','中文','bcd') res FROM dual;

res
-----------------------------------------
a中文bcd


```


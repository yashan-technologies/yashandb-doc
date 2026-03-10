```ebnf+diagram
concat_ws::= CONCAT_WS "(" separator "," (expr) {"," (expr)} ")"
```

CONCAT_WS函数将多个[expr](../通用SQL语法/expr)的值通过separator连接，返回一个拼接后的字符串。

本函数遵循如下规则：

- 本函数的参数须为[2,65535]个。

- 如果参数中包含二进制类型或bit类型，则函数返回类型为二进制类型，否则函数返回类型为字符类型。

- 如果separator为NULL，则函数返回结果为NULL。如果separator不为NULL，则函数拼接时会跳过为NULL的expr参数。

示例（HEAP表）

```sql

SELECT CONCAT_WS('1','2', '', 'ab', 'dc') res FROM DUAL;
res   
----- 
21ab1dc 

SELECT CONCAT_WS(12,null,null,'','a',null) res FROM DUAL;
res   
----- 
12a

```

```ebnf+diagram
space::= SPACE "(" expr ")"
```
SPACE函数返回[expr](../通用SQL语法/expr)个空格，返回类型为字符型。

本函数遵循如下规则：

- expr不能为JSON类型。

- 若expr为非整数类型，函数将对其进行四舍五入取整。

- 当expr的值大于65534时，函数返回值为CLOB类型，否则函数返回值为VARCHAR类型。

- 当expr的值为负数时，函数返回长度为0的空串。当expr值为NULL时，函数返回NULL。

- expr的值不能超过BIGINT最大值。

示例（HEAP表）

```sql
SELECT SPACE(10) res1, LENGTH(SPACE(10)) res2 FROM dual;

res1                                                       res2
----------------------------------------- ---------------------
                                                             10

```

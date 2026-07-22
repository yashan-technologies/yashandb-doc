```ebnf
strcmp = STRCMP "(" expr "," expr ")".
```

STRCMP函数用于比较两个[expr](../通用SQL语法/expr)，返回比较结果。

本函数遵循如下规则：

- 将两个expr都转换为varchar进行比较。
- 根据当前排序顺序，如果字符串相同，则函数返回0。如果第一个参数小于第二个参数，则函数返回-1，否则返回1。
- 当前默认字符序utf8mb4_general_ci。

示例（HEAP表）

```sql

SQL> select STRCMP('A',65) res;

res
-------------- 
             1

1 row fetched.
```

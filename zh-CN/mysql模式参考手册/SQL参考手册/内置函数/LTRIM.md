```ebnf
ltrim = LTRIM "(" expr ")".
```

LTRIM函数从左往右剔除源数据[expr](../通用SQL语法/expr.md)值中的空格并返回剔除后新数据，返回值类型与源数据一致。

本函数遵循如下规则：

- 当expr任意一个为二进制类型（BINARY、BLOB）或BIT类型时，函数将按二进制类型处理并且返回值为二进制类型数据，否则将按字符串类型处理并且返回值为字符串类型数据。

- 返回值类型是否为LOB取决于expr长度是否超过65534。

**expr**

expr为通用表达式，其值不能为JSON类型。

- 当expr的值为NULL时，函数返回NULL。

示例（单机HEAP表）

```sql
SQL> select length(LTRIM('   scdsf')) res1,LTRIM('   scdsf') res2;

                     res1 res2
------------------------- -----------------
                        5 scdsf

1 row fetched.
```

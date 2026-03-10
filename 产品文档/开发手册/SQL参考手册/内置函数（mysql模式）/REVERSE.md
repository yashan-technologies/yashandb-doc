```ebnf+diagram
reverse::= REVERSE "(" expr ")"
```

REVERSE函数用于反转[expr](../通用SQL语法/expr)的值。

本函数遵循如下规则：

- expr不能为JSON、XMLTYPE类型。

- 如果expr为二进制类型或bit类型，函数将以字节为单位对expr进行反转，输出结果为二进制类型。

- 如果expr为其他类型，函数会先将其转换为字符类型（转换失败将报错），再以字符为单位进行反转，输出结果为字符类型。

- 当expr为NULL时，函数返回NULL。

示例（单机HEAP表）

```sql
SELECT REVERSE('abcd中文字符efgh');

reverse('abcd中文字符efgh')
----------------------------------------------------------------
hgfe符字文中dcba



```


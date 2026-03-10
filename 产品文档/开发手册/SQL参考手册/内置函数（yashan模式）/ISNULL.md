```ebnf+diagram
isnull::= ISNULL "(" expr ")"
```

ISNULL函数判断参数[expr](../通用SQL语法/expr)的值是否为空，返回true或false。

expr的值可以为除ROWID、UDT类型外的其它任意数据类型。

对待空串和NULL均输出true。

示例

```sql
SELECT ISNULL('') nulla, ISNULL(3-3) nullb, ISNULL('      ') nullc FROM DUAL;
NULLA                NULLB                NULLC               
-------------------- -------------------- --------------------
true                 false                false
```


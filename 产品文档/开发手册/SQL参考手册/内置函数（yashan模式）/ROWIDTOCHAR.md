```ebnf+diagram
rowidtochar::= ROWIDTOCHAR "(" expr ")"
```

ROWIDTOCHAR函数将[expr](../通用SQL语法/expr)表示的ROWID类型数据转换为VARCHAR类型的字符串数据。

**expr**

expr的值须为ROWID类型数据，或者为字符型数据。对于其他类型，函数返回类型不支持。

当expr为NULL时函数返回NULL。

示例

```sql
SELECT ROWIDTOCHAR('2368:0:0:3164:0') rowid1 FROM DUAL;
ROWID1
-----------------
2368:0:0:3164:0
```


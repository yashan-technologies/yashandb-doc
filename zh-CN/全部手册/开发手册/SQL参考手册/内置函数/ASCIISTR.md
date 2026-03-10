```ebnf+diagram
asciistr::= ASCIISTR "(" expr ")"
```

ASCIISTR函数将[expr](../通用SQL语法/expr)表示的字符串转换为数据库字符集中的ASCII编码版本并返回。非ASCII字符将转换为形式\xxxx，其中xxxx表示UTF-16代码单元。

本函数遵循如下规则：

*   本函数不支持向量化计算。
*   字符'\\'会被转换为'\\005C'。
*   ASCII编码的字符会以数据库字符集编码版本返回。
*   非ASCII编码的字符会转换成形式\xxxx，其中xxxx表示UTF-16代码单元。
*   当expr的值为NULL时，函数返回NULL。

示例（HEAP表）

```sql
SELECT ASCIISTR('HELLO——WORLD') FROM DUAL;

ASCIISTR('HELLO——WORLD')
----------------------------
HELLO\2014\2014WORLD

```

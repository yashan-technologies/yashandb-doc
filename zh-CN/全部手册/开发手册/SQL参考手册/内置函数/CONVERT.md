```ebnf+diagram
convert::= CONVERT "(" expr "," dest_char_set ["," source_char_set] ")"
```
CONVERT函数用于将表达式[expr](../通用SQL语法/expr)从source_char_set字符集转换为dest_char_set字符集。

本函数遵循如下规则：

- 本函数仅适用于HEAP表。

- 本函数涉及字符集转换，转换后的字符串与lengthb结合使用结果准确，与其他函数（例如length、substr等）的嵌套使用可能出现乱码或转换报错等不可控行为，结果不准确。

**expr**

原数据，通用表达式。

- 当expr的值为CLOB类型时返回值为CLOB类型，当expr的值为NCLOB类型时返回值为NCLOB类型，当expr值为NCHAR/NVARCHAR类型时返回值为NVARCHAR类型，其余场景返回值为VARCHAR类型。

- 当expr的值为NULL时，函数返回NULL。

**dest_char_set**

目标字符集，通用表达式。

- 只能为YashanDB支持字符集[字符集配置](../../../数据库管理/实例管理/数据库字符集配置)，否则报错。

- 当dest_char_set的值为NULL时，函数返回NULL。

**source_char_set**

初始字符集，通用表达式。

- 只能为YashanDB支持字符集[字符集配置](../../../数据库管理/实例管理/数据库字符集配置)，否则报错。

- 若source_char_set缺省，则默认为当前数据库字符集。

- source_char_set的值为NULL时，函数返回NULL。

示例（HEAP表）

```plsql
SET serveroutput ON;
DECLARE
a VARCHAR(1000) := '——';
BEGIN
dbms_output.put_line(lengthb(a));
a := CONVERT(a,'zhs16gbk','al32utf8');
dbms_output.put_line(lengthb(a));
END;
/

--result
6
4
```

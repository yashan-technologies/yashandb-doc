```ebnf+diagram
ascii::= ASCII "(" expr ")"
```

ASCII函数将[expr](../通用SQL语法/expr)表示的一个字符转换为ASCII码，返回一个INT类型的数值。

本函数遵循如下规则：

*   只支持对有ASCII编码的字符进行转换，其他非ASCII编码的字符（例如中文等）不能转换。
*   expr的值必须为数值型、布尔型、日期时间型或字符型，否则返回类型不支持。
*   若expr的值为多字符，不会报错，函数将只对第一个字符进行ASCII转换并返回。
*   当expr的值为NULL时，函数返回NULL。


示例

```sql
-- 函数将数值23.35转换为字符'23.35',并返回首字符'2'的ASCII值
SELECT ASCII(23.35) result,
TYPEOF(ASCII(23.35)) result_type
FROM DUAL;
      RESULT RESULT_TYPE
------------ -----------
          50 integer
```


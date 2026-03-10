```ebnf+diagram
last_day::= LAST_DAY "(" expr ")"
```

LAST_DAY函数返回[expr](../通用SQL语法/expr)表示的日期所在月份的最后一天的日期值，返回类型为DATE，且与DATE_FORMAT参数所指定格式一致。

**expr**

expr的值须为DATE、TIME、TIMESTAMP、时区类型，或可转换为DATE类型的字符型。其中，当为TIME类型时，函数返回NULL。

当expr的值为NULL时，函数返回NULL。

示例

```sql
SELECT LAST_DAY('2020-02-01') res FROM DUAL;
RES
------------------------------------------------
2020-02-29 00:00:00
                                 
SELECT LAST_DAY(SYSDATE+20) res FROM DUAL;
RES            
--------------------------------
2021-12-31 18:14:21
```


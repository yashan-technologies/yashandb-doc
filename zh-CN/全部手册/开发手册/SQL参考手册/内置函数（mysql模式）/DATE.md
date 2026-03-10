```ebnf+diagram
date::= DATE "(" expr ")"
```

DATE函数用于提取[expr](../通用SQL语法/expr)日期部分的值并返回。

**expr**

expr的值须为或可转为DATE/TIMESTAMP/TIME的数据类型。

当expr的值为NULL时，函数返回NULL。

示例（单机HEAP表）

```sql
SELECT DATE('2024-01-01 01:02:03') RES,TYPEOF(DATE('2024-01-01 01:02:03')) RES_TYPE FROM DUAL;

RES                                              RES_TYPE
------------------------------------------------ ----------------------------------------------------------------
2024-01-01 00:00:00                              date
```

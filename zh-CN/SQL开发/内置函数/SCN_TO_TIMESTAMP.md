```ebnf+diagram
scn_to_timestamp::= SCN_TO_TIMESTAMP "(" expr ")"
```

SCN_TO_TIMESTAMP函数将[expr](../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)表示的SCN号转换为时间戳数据。

expr的值须为BIGINT类型数据，或者可转换为BIGINT的其他整数类型数据（具体包括TINYINT、SMALLINT、INT、BIGINT四种数据类型），否则返回类型不支持。

本函数不支持传递null作为参数。

示例

```sql
SELECT SCN_TO_TIMESTAMP(247677771776000000) timestamp1, SCN_TO_TIMESTAMP(555+3) timestamp2 FROM DUAL;
TIMESTAMP1                         TIMESTAMP2
---------------------------------- ------------------------------
2021-12-01 04:43:26.000000         2020-01-01 08:00:00.000000
```

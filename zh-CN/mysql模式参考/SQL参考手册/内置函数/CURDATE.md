```ebnf+diagram
curdate::= CURDATE "("")"
current_date::= CURRENT_DATE "("")"
```

CURDATE/CURRENT_DATE函数返回数据库所在的操作系统设置的当前日期，返回值类型为DATE，格式默认为'YYYY-MM-DD'。

CURDATE/CURRENT_DATE函数始终返回语句开始执行时的当前日期，若同一SQL语句中存在多个CURDATE/CURRENT_DATE函数，每个CURDATE/CURRENT_DATE函数的返回值将完全一致。

示例（单机HEAP表）

```sql
SELECT CURDATE(), SLEEP(1), CURDATE();

CURDATE()            SLEEP(1)      CURRENT_DATE()
-------------------- ------------- --------------------
2024-12-26           0             2024-12-26

```

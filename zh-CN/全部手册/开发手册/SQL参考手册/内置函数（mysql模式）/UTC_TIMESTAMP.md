```ebnf+diagram
utc_timestamp::= UTC_TIMESTAMP "(" [fsp] ")"
```

UTC_TIMESTAMP函数返回数据库所在的操作系统设置的当前协调世界时（UTC），返回值类型为TIMESTAMP，格式为'YYYYMMDD HH:MI:SS'。

可选参数fsp为0-6的整型数字，表示返回值中小数的有效位数，若不指定参数，默认为0。

UTC_TIMESTAMP函数始终返回语句开始执行的UTC时间，若同一SQL语句中存在多个UTC_TIMESTAMP函数，每个UTC_TIMESTAMP函数的返回值将完全一致。

示例（单机HEAP表）

```sql
SELECT UTC_TIMESTAMP(), SLEEP(1), UTC_TIMESTAMP();

UTC_TIMESTAMP()                 SLEEP(1) UTC_TIMESTAMP()
-------------------------- ------------- ---------------------------
2024-12-26 23:55:31.000000             0 2024-12-26 23:55:31.000000

```

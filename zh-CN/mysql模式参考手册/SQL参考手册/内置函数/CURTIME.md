```ebnf
curtime = CURTIME "(" [fsp] ")".
current_time = CURRENT_TIME "(" [fsp] ")".
```

CURTIME/CURRENT_TIME函数返回数据库所在的操作系统设置的当前时间，返回值类型为TIME，格式默认为'HH24:MI:SS.ff'。

可选参数fsp为0-6的整型数字，表示返回值中小数的有效位数，若不指定参数，默认为0。

CURTIME/CURRENT_TIME函数始终返回语句开始执行时的当前时间，若同一SQL语句中存在多个CURTIME/CURRENT_TIME函数，每个CURTIME/CURRENT_TIME函数的返回值将完全一致。

示例（单机HEAP表）

```sql
SELECT CURTIME(6), SLEEP(1), CURRENT_TIME(6);

CURTIME(6)           SLEEP(1)      CURRENT_TIME(6)
-------------------- ------------- --------------------
11:08:37.350155      0             11:08:37.350155

1 row fetched.
```

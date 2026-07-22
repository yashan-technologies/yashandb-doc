```ebnf
sysdate = SYSDATE ["(" [integer] ")"].
```

SYSDATE函数返回数据库所在的操作系统设置的当前日期，其返回类型为DATE，且与DATE\_FORMAT参数所指定格式一致。

以下三种形式的SYSDATE同义，返回结果无差别：

- `SYSDATE`
- `SYSDATE()`
- `SYSDATE(integer)`，integer必须为一个0~6之间的整数字面量。

如果一个SQL语句中出现了多个SYSDATE函数，在该语句执行过程中将只调用一次函数，即保证多个SYSDATE函数返回的是相同一个日期值。

示例

```sql
SELECT SYSDATE res1,SYSDATE() res2,SYSDATE(5) res3 FROM DUAL;
RES1                    RES2                     RES3                   
----------------------- ------------------------ ----------------------
2022-11-02 03:07:43     2022-11-02 03:07:43      2022-11-02 03:07:43   
```

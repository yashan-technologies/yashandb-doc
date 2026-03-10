```ebnf+diagram
localtimestamp::= LOCALTIMESTAMP ["(" [integer] ")"]
```

LOCALTIMESTAMP函数返回会话设置的时区对应的时间戳，其返回类型为TIMESTAMP，且与TIMESTAMP_FORMAT参数所指定格式一致。

本函数不支持向量化计算。

LOCALTIMESTAMP有以下三种形式：

- `LOCALTIMESTAMP`
- `LOCALTIMESTAMP()`
- `LOCALTIMESTAMP(integer)`，integer必须为一个0~9之间的整数字面量，表示保留的微秒位数，舍去的位按四舍五入。

如果一个SQL语句中出现了多个LOCALTIMESTAMP函数，在该语句执行过程中将只调用一次函数，即保证多个LOCALTIMESTAMP函数返回的是相同一个时间戳值。

当LOCALTIMESTAMP函数参与运算时，其运算规则与TIMESTAMP类型一致，见[算术运算符](../运算符/算术运算符)章节描述。

示例（HEAP表）

```sql
SELECT LOCALTIMESTAMP res1,LOCALTIMESTAMP() res2,LOCALTIMESTAMP(9) res3,LOCALTIMESTAMP+1 res4 FROM DUAL;
RES1                                                             RES2                                                             RES3                                                             RES4                             
---------------------------------------------------------------- ---------------------------------------------------------------- ---------------------------------------------------------------- -------------------------------- 
2023-05-03 20:38:06.665584                                       2023-05-03 20:38:06.665584                                       2023-05-03 20:38:06.665584                                       2023-05-04                      
```

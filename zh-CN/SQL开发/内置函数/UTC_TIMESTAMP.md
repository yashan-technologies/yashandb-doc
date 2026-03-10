```ebnf+diagram
utc_timestamp::= UTC_TIMESTAMP ["(" [integer] ")"]
```

UTC_TIMESTAMP函数返回数据库所在的操作系统设置的当前协调世界时，其返回类型为TIMESTAMP，且与TIMESTAMP_FORMAT参数所指定格式一致。

UTC_TIMESTAMP有以下三种形式：

- `UTC_TIMESTAMP`
- `UTC_TIMESTAMP()`
- `UTC_TIMESTAMP(integer)`，integer必须为一个0~9之间的整数字面量，表示保留的微秒位数，舍去的位按四舍五入。

如果一个SQL语句中出现了多个UTC_TIMESTAMP函数，在该语句执行过程中将只调用一次函数，即保证多个UTC_TIMESTAMP函数返回的是相同一个时间戳值。

当UTC_TIMESTAMP函数参与运算时，其运算规则与TIMESTAMP类型一致，见[算术运算符](../../全部手册/开发手册/SQL参考手册/运算符/算术运算符)章节描述。

示例

```sql
SELECT UTC_TIMESTAMP res1,UTC_TIMESTAMP() res2,UTC_TIMESTAMP(9) res3 FROM DUAL;
RES1                                                             RES2                                                             RES3                                                             
---------------------------------------------------------------- ---------------------------------------------------------------- ---------------------------------------------------------------- 
2023-05-04 06:24:04.636637                                       2023-05-04 06:24:04.636637                                       2023-05-04 06:24:04.636637                                      
```

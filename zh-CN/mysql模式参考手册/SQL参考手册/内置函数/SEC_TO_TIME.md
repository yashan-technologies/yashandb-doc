```ebnf
sec_to_time = SEC_TO_TIME "(" seconds ")" .
```

SEC_TO_TIME函数输入秒数，计算并返回TIME类型。

**seconds**

表示秒数，BigInt类型或可转化为BIGINT类型的其他类型。

示例（HEAP表）

```sql
-- example via yasql
select sec_to_time(182378) res;

res  
-------------------- 
50:39:38.000000

-- example via MySQL Client
select sec_to_time(182378) res;
+---------------------+
| res                 |
+---------------------+
| 50:39:38            |
+---------------------+
```

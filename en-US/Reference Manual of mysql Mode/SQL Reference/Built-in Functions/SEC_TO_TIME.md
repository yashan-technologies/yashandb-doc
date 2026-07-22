```ebnf
sec_to_time = SEC_TO_TIME "(" seconds ")" .
```

The SEC_TO_TIME function takes seconds as input and calculates and returns a TIME type.

**seconds**

Represents seconds, which can be of BigInt type or other types that can be converted to BIGINT.

***Example*** for  Heap tables

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

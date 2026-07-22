```ebnf
substring_index = SUBSTRNG_INDEX "(" expr ", " delim ", " count ")".
```

SUBSTRING_INDEX函数返回源字符串中出现第count次delim字符之前的子串。

**expr, delim**

expr[expr](../通用SQL语法/expr.md)表示源字符串，delim表示定界符，二者均为字符型或可转换为字符型的其他类型。定界符匹配大小写敏感。

**count**

count表示定界符子源字符串中出现的次数，正数表示从左计数，负数表示从右计数，须为BIGINT类型或可转换为BIGINT类型的其他类型。

```sql
select substring_index('abc.def.hij', '.', 2) res;

res 
-------------------------------------- 
abc.def
    
select substring_index('abc.def.hij', '.', -2) res;

res 
--------------------------------------- 
def.hij
```

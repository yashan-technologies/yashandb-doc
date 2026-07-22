```ebnf
substring_index = SUBSTRNG_INDEX "(" expr ", " delim ", " count ")".
```

The SUBSTRING_INDEX function returns the substring from the source string before the count-th occurrence of the delim character.

**expr, delim**

expr[expr](../General SQL Syntax/expr) represents the source string, and delim represents the delimiter. Both must be of character type or another type that can be converted to character type. Delimiter matching is case-sensitive.

**count**

count represents the number of occurrences of the delim substring within the source string. A positive number indicates counting from the left, while a negative number indicates counting from the right. It must be of BIGINT type or another type that can be converted to BIGINT type.

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

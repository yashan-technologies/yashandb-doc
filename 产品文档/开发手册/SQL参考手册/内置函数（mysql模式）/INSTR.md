```ebnf+diagram
instr::= INSTR "(" expr ", " sub_character ")"
```

INSTR函数与[LOCATE](LOCATE)函数功能相同，但目标字符串参数位置不同且没有position参数，从源字符串[expr](../通用SQL语法/expr)的第1位开始查找目标字符串sub_character，比较规则为大小写敏感，返回第1次出现sub_character的位置值，返回值为BIGINT类型，未查找到则返回0。

示例（单机HEAP表）

```sql
SELECT INSTR('abcd', 'b');

instr('abcd', 'b') 
--------------------- 
                    2
```


```ebnf+diagram
locate::= LOCATE "(" sub_character "," expr ["," position] ")"
```

LOCATE函数从源字符串[expr](../通用SQL语法/expr)的第position位开始查找目标字符串sub_character，比较规则为大小写敏感，返回第1次出现sub_character的位置值，返回值为BIGINT类型，未查找到则返回0。

**expr、sub_character**

expr指定源字符串，sub_character指定目标字符串，二者均为通用表达式，其值均须为字符型、二进制类型、可转化为字符型的其他类型。

- 当expr的值为NULL时，函数返回NULL。

- 当sub_character的值为NULL时，函数返回NULL。  

- 二者均不能为超过65534字节的LOB类型数据。

**position**

指定开始查找的偏移量（即起始位置），可省略，默认为1。position为与expr相同的通用表达式，须为数值型数据或可转换为NUMBER类型的其他类型数据。

- position的值应为整数或可被转换为整数（浮点类型或带有小数的NUMBER类型将四舍五入取整），内部处理时统一转换为BIGINT类型。正整数表示从前往后自起始位置开始查找，0或负整数则不执行查找并返回0。

- 当position为NULL时，函数返回0。

示例（单机HEAP表）

```sql
SELECT LOCATE('c', 'abcd', null);

locate('c', 'abcd', null) 
------------------------- 
                        0

SELECT LOCATE('b', 'abcd');

  locate('b', 'abcd') 
--------------------- 
                    2
```


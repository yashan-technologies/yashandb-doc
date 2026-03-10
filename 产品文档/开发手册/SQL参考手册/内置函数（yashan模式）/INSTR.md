```ebnf+diagram
instr::= INSTR "(" expr "," sub_character ["," position ["," occurrence]] ")"
```

INSTR函数从源字符串[expr](../通用SQL语法/expr)的第position位开始查找目标字符串sub_character，返回第occurrence次出现sub_character的位置值，返回值为BIGINT类型。未查找到则返回0。

**expr、sub_character**

expr指定源字符串，sub_character指定目标字符串，二者均为通用表达式。expr必须是字符型、CLOB、NCLOB，或除XMLTYPE类型外可转化为字符型的其他类型。sub_character须为字符型，或除XMLTYPE类型外可转化为字符型的其他类型。

- 当expr的值为NULL时，函数返回NULL。

- 当sub_character的值为NULL时，函数返回NULL。

- 当expr的值为empty_clob()时，如果sub_character是NULL或者隐式转换为字符串以后是NULL则函数返回NULL，否则函数返回0。

- 当expr的值为empty_blob()时，函数返回NULL。

**position**

指定开始查找的偏移量（即起始位置），可省略，默认为1。position为与expr相同的通用表达式，须为数值型数据或可转换为NUMBER类型的其他类型数据。

- position的值应为整数或可被转换为整数，取值范围为[-9223372036854775808,9223372036854775807]。正整数表示从前往后自起始位置开始查找，负整数表示从后向前自起始位置开始查找。

    - 当position的值为带有小数的NUMBER类型时，函数截断其小数位保留整数位。

    - 当position的值为浮点类型时，如果expr为CLOB、NCLOB、BLOB、JSON类型，函数将对position的值直接截断取整。如果expr为其他类型，函数将对position的值奇进偶舍取整。

- 当position的值为0时，函数不执行查找并返回0。

- 当position为NULL时，函数返回NULL。

**occurrence**

指定返回子字符串在expr中出现的第occurrence次的位置，可省略，默认为1。occurrence为与expr相同的通用表达式，其值须为数值型数据或可转换为NUMBER类型的其他类型数据。

- occurrence的值必须为正整数或可被转换为正整数，取值范围为[1,9223372036854775807]。

    - 当occurrence的值为带有小数的NUMBER类型时，函数截断其小数位保留整数位。

    - 当occurrence的值为浮点类型时，函数将其奇进偶舍至整数。

- 当occurrence的值为NULL时，函数返回NULL。  

示例

```sql
SELECT INSTR('This is a playlist', 'is') POS FROM DUAL;
                  POS
---------------------
                    3
 
SELECT INSTR('This is a playlist', 'is', 1, 2) POS FROM DUAL;
                  POS
---------------------
                    6
 
SELECT INSTR('This is a playlist', 'is', 1, 3) POS FROM DUAL;
                  POS
---------------------
                   16
 
SELECT INSTR('This is a playlist', 'is', -2, 3) POS FROM DUAL;
                  POS
---------------------
                    3
 
SELECT INSTR('This is a playlist', 'is', 5, 3) POS FROM DUAL;
                  POS
---------------------
                    0
```


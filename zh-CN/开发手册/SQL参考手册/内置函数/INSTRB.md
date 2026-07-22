```ebnf
instrb = INSTRB "(" expr "," sub_character ["," position ["," occurrence]] ")".
```
INSTRB函数从源字符串[expr](../通用SQL语法/expr)的第position个字节开始查找目标字符串sub_character，返回第occurrence次出现sub_character的位置值，返回值为BIGINT类型。未查找到则返回0。

本函数不支持向量化计算。

**expr, sub_character**

expr指定源字符串，sub_character指定目标字符串，二者均为通用表达式，其值均须为字符型或可转换为字符型的其他类型。

- expr、sub_character不能为超过65534字节的BFILE、LOB类型数据。

- 当expr的值为NULL时，函数返回NULL。

- 当sub_character的值为NULL时，函数返回NULL。

**position**

指定开始查找的偏移量（即起始位置），可省略，默认为1。position为与expr相同的通用表达式，其值须为数值型数据或可转换为NUMBER类型的其他类型数据。

- position的值应为整数或可被转换为整数，取值范围为[-4294967295,4294967295]。正整数表示从前往后自起始位置开始查找，负整数表示从后向前自起始位置开始查找。

    - 当position的值为带有小数的NUMBER类型时，函数截断其小数位保留整数位。

    - 当position的值为浮点类型时，函数将其奇进偶舍至整数。

- 当position的值为0时，函数不执行查找并返回0。

- 当position为NULL时，函数返回NULL。

**occurrence**

指定返回子字符串在expr中出现的第occurrence次的位置，可省略，默认为1。occurrence为与expr相同的通用表达式，其值须为数值型数据或可转换为NUMBER类型的其他类型数据。

- occurrence的值必须为正整数或可被转换为正整数，取值范围为[1,4294967295]。

    - 当occurrence的值为带有小数的NUMBER类型时，函数截断其小数位保留整数位。

    - 当occurrence的值为浮点类型时，函数将其奇进偶舍至整数。

- 当occurrence的值为NULL时，函数返回NULL。  

示例（HEAP表）

```sql
SELECT INSTRB('abc', 'a') a FROM DUAL;

                    A
---------------------
                    1

SELECT INSTRB('abc', 'b') b FROM DUAL;

                    B
---------------------
                    2
      
SELECT INSTRB('————abc','——',7) c FROM DUAL;

                    C
---------------------
                    7

SELECT INSTRB('————abc','——',7.4) d FROM DUAL;

                    D
---------------------
                    7
```

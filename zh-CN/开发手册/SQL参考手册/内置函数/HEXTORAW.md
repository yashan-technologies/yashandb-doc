```ebnf
hextoraw = HEXTORAW "(" expr ")".
```

HEXTORAW函数将[expr](../通用SQL语法/expr)表述的十六进制字符串转为二进制数据，返回一个RAW类型的结果。

函数将expr中的每两个十六进制字符子串转成一个字节，如字符串'ab'转成'0xab'；如果expr的字符数量为奇数，函数将先补充4个都是0的连续位，如字符串'abc'转成'0x0abc'。

本函数不支持向量化计算。

expr的值须为字符型或可转换为字符型的其他类型，但不允许为除BLOB外的其它LOB类型数据。当expr的值为NULL时，函数返回NULL。

expr中包含不属于十六进制表述范围（0-9、a-f、A-F）的字符时，函数报错。

当本函数用于插入或更新一个RAW列字段时，如果expr的字节长度超过RAW列宽度的2倍，函数报错。

示例（HEAP表）

```sql
SELECT HEXTORAW('1234abcd') res FROM DUAL;
RES
-------------------- 
1234ABCD

SELECT HEXTORAW('') res FROM DUAL;
RES
-------------- 
              
--包含不属于十六进制表述范围（0-9、a-f、A-F）的字符时报错
SELECT HEXTORAW('1234abcddfdfh') res FROM DUAL;
YAS-00223 invalid hex number

--奇数位在前面补0
SELECT HEXTORAW('1abcdef') res FROM DUAL;
RES
--------
01ABCDEF

--超过RAW列宽度的2倍时报错
CREATE TABLE tb_raw(c1 RAW(4));
INSERT INTO tb_raw VALUES('abcdabcd');
1 row affected.
INSERT INTO tb_raw VALUES('abcdabcda');
YAS-04008 C1 size exceeding limit 4
```

```ebnf
soundex = SOUNDEX "(" expr ")".
```

SOUNDEX函数返回其参数expr[通用表达式](../通用SQL语法/expr)的[Soundex编码](https://www.archives.gov/research/census/soundex)，expr须为字符型或可以转换为字符型的其他类型。


本函数的转换规则如下：

1. 找到字符串的首个汉字、英文字母或特殊符号作为起始转换位置，并将原字符作为返回值的第一个字符。

2. 按照下表转换规则将字符串中的英文字母进行转换，作为返回值的后续字符，除英文字母外其他字符将被忽略。

   |字母|编码|
   |------|----|
   |A, E, I, O, U, H, W, Y| 0 |
   |B, F, P, V| 1 |
   |C, G, J, K, Q, S, X, Z| 2 |
   |D, T|3|
   |L |4|
   |M, N|5|
   |R|6|

3. 对转换后的字符串中连续的数值部分进行去重保留一位，并删除所有的0。

4. 返回时只返回前4个字符，如果字符个数不到4个，则在末尾补0。


本函数使用约束如下：

- 当expr为RAW、BLOB和BIT时，返回值为RAW类型；当expr为TEXT类型时返回TEXT类型；当expr为其他类型时，返回值为VARCHAR类型。
- 当expr隐式转换成字符型后的值中不包含满足转换规则的字符时，函数返回NULL。
- 当expr为NULL时，函数返回NULL。

示例（HEAP表）

```sql
SELECT SOUNDEX(NULL) from dual;

SOUNDEX(NULL)
------------- 

SELECT SOUNDEX('SSHS') from dual;

SOUNDEX('SSHS')
--------------- 
       S200
SQL> SELECT SOUNDEX('SMYTHE') from dual;

SOUNDEX('SMYTHE')
----------------- 
       S530
SELECT SOUNDEX('SMITH') from dual;

SOUNDEX('SMITH')
---------------- 
       S530  
```

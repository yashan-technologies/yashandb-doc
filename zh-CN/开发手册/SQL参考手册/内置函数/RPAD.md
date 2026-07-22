```ebnf
rpad = RPAD "(" expr "," pad_length ["," pad_character] ")".
```

RPAD从右边对[expr](../通用SQL语法/expr)表示的字符串进行指定字符、指定长度的填充，得到一个新字符串。

**expr**

expr的值须为字符型或可转换为字符型的其他类型（LOB、BFILE类型支持隐式转换）。

- 在向量化执行引擎中，expr不能为LOB类型的行外存储数据。

- expr不能为超过65534字节的BFILE、LOB类型数据。

- 当expr为NCLOB、NCHAR、NVARCHAR类型时，返回值为NVARCHAR类型，其余场景返回值为VARCHAR类型。

- 当expr的值为NULL时，函数返回NULL。

**pad\_length**

指定填充后字符串的最终长度，pad_length为与expr相同的通用表达式，须为除BIT外数值型数据或可转换为NUMBER类型的其他类型数据，取值范围[-9223372036854775808,65534]。

- 当pad_length的值为NULL、0或负数时，函数返回NULL。  

- 当pad_length的值为小数时，函数截断其小数位保留整数位。

- 当pad_length的值小于等于expr字符串长度时，其效果等同于对expr字符串进行截取，函数返回从左到右对expr按该长度进行截取的子字符串。

- 当pad_length的值大于expr字符串长度时，其效果才是对expr字符串进行填充。

**pad\_character**

指定要填充的内容，可省略。pad_character为与expr相同的通用表达式，须为字符型数据或可转换为字符型的其他类型数据（LOB、BFILE类型支持隐式转换）。

- 省略pad_character时，默认填充空格。

- 指定pad_character时，函数将循环从左至右读取pad_character的字符并填充到expr的左边，直到满足pad_length的长度要求为止。

- 在向量化执行引擎中，pad_character不能为LOB类型的行外存储数据。

- 当pad_character的值为NULL时，函数返回NULL。

  

示例

```sql
SELECT RPAD('深圳',16,'NIHAO') AS res FROM DUAL;
RES                  
---------------------
深圳NIHAONIHAONIHA
 
SELECT RPAD('深圳',1,'NIHAO') AS res FROM DUAL;
RES
-----
深
 
SELECT RPAD('深圳',5,'NIHAO') AS res FROM DUAL;
RES
-------------
深圳NIH
 
SELECT RPAD('深圳',16) AS res1 FROM DUAL;
RES1
---------------------
深圳  
  
SELECT RPAD('深圳',1.999) AS res1 FROM DUAL;
RES1
-----
深
  
SELECT RPAD('深圳','1.999') AS res1 FROM DUAL;
RES1
-----
深
```

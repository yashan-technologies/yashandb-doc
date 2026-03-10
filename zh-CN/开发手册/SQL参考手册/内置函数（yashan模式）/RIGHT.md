```ebnf+diagram
right::= RIGHT "(" expr "," length ")"
```

RIGHT函数将[expr](../通用SQL语法/expr)表示的字符串从右边截取指定长度，得到一个子字符串并将其返回。

**expr**

expr的值须为字符型或可转换为字符型的其他类型（LOB、BFILE类型支持隐式转换）。

- 在向量化执行引擎中，expr不能为LOB类型的行外存储数据。

- expr不能为超过65534字节的BFILE、LOB类型数据。

- 当expr为NCLOB/NCHAR/NVARCHAR类型时，返回值为NVARCHAR。其余场景返回值为VARCHAR。

- 当expr的值为NULL时，函数返回NULL。

**length**

指定字符串截取的长度，length为与expr相同的通用表达式，须为除BIT外数值型数据，或可转换为NUMBER类型的其他类型数据，取值范围[-2147483648,2147483647]。

- 当length的值为NULL，0或负数时，函数返回NULL。  

- 当length值为小数时，函数将先对其进行取整，规则如下：

  - length为NUMBER型或者浮点型时：四舍五入取整。

  - length为可转换为NUMBER的其他类型时：截取整数部分。

- 若length值大于expr字符串长度，则将其按expr字符串长度值处理。

示例

```sql
SELECT RIGHT(SYSDATE+1,4.99) res FROM DUAL;
RES                                            
----------------------------------------------------------------
54:23 
 
SELECT RIGHT(SYSDATE+1,'4.99') res FROM DUAL;
RES                                            
----------------------------------------------------------------
3:24
```

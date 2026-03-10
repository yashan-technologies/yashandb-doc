```ebnf+diagram
left::= LEFT "(" expr "," length ")"
```

LEFT函数将[expr](../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)的值从左边截取指定长度，得到一个子字符串并将其返回。

**expr**

expr的值须为字符型或可转换为字符型的其他类型。

- 在向量化执行引擎中，expr不能为LOB类型的行外存储数据。

- expr不能为超过65534字节的BFILE、LOB类型数据。

- 当expr为NCLOB/NCHAR/NVARCHAR时返回值为NVARCHAR类型，其余场景返回值为VARCHAR类型。

- 当expr的值为NULL时，函数返回NULL。

**length**

指定字符串截取的长度，length为与expr相同的通用表达式，须为除BIT外数值型数据或可转换为NUMBER类型的其他类型数据，取值范围为[-2147483648,2147483647]。

- 当length的值为NULL或[-2147483648,0]时，函数返回NULL。  

- 当length值为小数时，函数将先对其进行取整，规则如下：

  - NUMBER型或浮点型：四舍五入取整。  

  - 可转换为NUMBER的其他类型：向下取整（即截取整数部分）。
  
- 当length值大于expr字符串长度时，将其按expr字符串长度值处理。

示例

```sql
SELECT LEFT(SYSDATE+1,4.99) res FROM DUAL;
RES                                            
----------------------------------------------------------------
2022-          
 
SELECT LEFT(SYSDATE+1,'4.99') res FROM DUAL;
RES                                            
----------------------------------------------------------------
2022
```

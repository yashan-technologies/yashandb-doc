```ebnf+diagram
rpad::= RPAD "(" expr "," pad_length "," pad_expr ")"
```
RPAD函数从右边对源数据[expr](../通用SQL语法/expr)的值进行pad_length指定长度、pad_expr指定内容的填充并返回填充所得新数据。

本函数遵循如下规则：

- 当expr、pad_expr任意一个为二进制类型（BINARY、BLOB）或BIT类型时，函数将按二进制类型处理并且返回值为二进制类型数据，否则将按字符串类型处理并且返回值为字符串类型数据。

- 返回值类型是否为LOB取决于pad_length是否超过65534。

**expr**

expr为通用表达式，其值不能为JSON类型。

- 当expr的值为NULL时，函数返回NULL。  

**pad_length**

指定进行填充后的最终长度，pad_length为与expr相同的通用表达式，须为能转为BIGINT的类型，取值范围为[-9223372036854775808,9223372036854775807]。

- 当pad_length的值为NULL或负数时，函数返回NULL。  

- 当pad_length的值为小数时，函数会先对其四舍五入取整。

- 当pad_length的值小于等于expr长度时，其效果等同于对expr进行截取，函数返回从左到右对expr按该长度进行截取的子字符串或子二进制串。

 **pad_character**

指定要填充的内容。pad_expr为与expr相同的通用表达式，其值不能为JSON类型。

- 指定pad_expr，函数将循环从左至右读取pad_expr的字符或字节并填充到expr的右边，直到满足pad_length的长度要求为止。

- 当pad_expr为空串或空LOB，且expr长度小于pad_length，函数返回NULL。

- 当pad_expr的值为NULL时，函数返回NULL。  

示例（单机HEAP表）

```sql
SELECT RPAD('a',3,'bcdef');

rpad('a',3,'bcdef') 
------------------- 
abc                


SELECT RPAD('abcdef',3,'g');

rpad('abcdef',3,'g') 
-------------------- 
abc                 

```


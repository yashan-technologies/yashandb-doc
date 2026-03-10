```ebnf+diagram
left::= LEFT "(" expr "," length ")"
```

LEFT函数将源数据[expr](../通用SQL语法/expr)的值从左边截取length指定长度并返回截取所得子串，返回值类型与源数据一致。

本函数遵循如下规则：

- 当expr为二进制类型（BINARY、BLOB）或BIT类型时，函数将按二进制类型处理并且返回值为二进制类型数据，否则将按字符串类型处理并且返回值为字符串类型数据。

- 返回值类型是否为LOB取决于expr长度值与length值的最小值是否超过65534。

**expr**

expr为通用表达式，其值不能为JSON类型。

- 当expr的值为NULL时，函数返回NULL。

**length**

指定字符串截取的长度，length为与expr相同的通用表达式，须为可以转成BIGINT的数据类型，取值范围为[-9223372036854775808,9223372036854775807]。

- 当length的值为NULL或[-9223372036854775808,0]时，函数返回空串。  

- 当length值为小数时，函数会先对其四舍五入取整。
  
- 当length值大于expr字符串长度时，直接返回原字符串。

示例（HEAP表）

```sql
SELECT LEFT('abcde', 3) res;

res 
-------------- 
abc           

```

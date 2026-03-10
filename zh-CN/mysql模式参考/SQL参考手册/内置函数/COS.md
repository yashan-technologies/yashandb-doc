```ebnf+diagram
cos::= COS  "(" expr ")" 
```

COS函数返回给定参数的余弦值，参数为以弧度表示的角度，大小本身无限制（只受限于其所属数据类型所规定范围），函数将返回一个大小在区间[-1,1]的DOUBLE类型数据。

其中[expr](../通用SQL语法/expr)的值为数值型或可以转换为DOUBLE类型的字符型。对于其他类型，函数返回类型不支持。

当expr的值为NULL时，函数返回NULL。

不允许对BLOB和TEXT类型数据使用此函数。

示例（单机HEAP表）

```sql
SELECT COS(30*3.1415926/180) res FROM DUAL;
        res 
----------- 
  8.66E-001
    
SELECT COS(45*3.1415926/180) res FROM DUAL;
        res 
----------- 
 7.071E-001
    
SELECT COS(60*3.1415926/180) res FROM DUAL;
        res 
----------- 
   5.0E-001
```

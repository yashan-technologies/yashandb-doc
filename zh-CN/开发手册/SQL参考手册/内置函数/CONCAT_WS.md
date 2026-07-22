```ebnf
concat_ws = CONCAT_WS "(" separator "," expr {"," expr} ")".
```

CONCAT_WS函数将多个[expr](../通用SQL语法/expr)的值通过separator连接，返回一个拼接后的字符串。

本函数遵循如下规则：

- 本函数的参数须为[2,32768]个。
- expr中包含NCHAR或NVARCHAR类型数据时，返回值为NVARCHAR类型，否则为VARCHAR类型。
- 当除了分隔符外，只有一个非空参数时，函数只返回该非空参数（不包含分隔符）。
- 当需要拼接的字符串为NULL或''时，函数不会对该字符串进行拼接（且不会连续拼接分隔符）。
- 分隔符为NULL或''时，函数返回NULL。

**separator**

分隔符，必须为字符型或字符串，但不能为超过65534字节的BFILE、LOB类型数据。

**expr**

待拼接的数据，可以为数值型、字符型、布尔型或日期时间型。

当expr的值为CHAR类型时，函数会先按其定义长度补齐空格，再将其参与拼接。

示例

```sql
SELECT CONCAT_WS('1','2', '', 'ab', 'dc') res FROM DUAL;
RES   
----- 
21ab1dc 

SELECT CONCAT_WS(12,null,null,'','a',null) res FROM DUAL;
RES   
----- 
a  
```

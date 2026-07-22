```ebnf
rawtohex = RAWTOHEX "(" expr ")".
```

RAWTOHEX函数用于将[expr](../通用SQL语法/expr)表述的二进制数据转换为十六进制字符串，返回结果为VARCHAR类型。

本函数遵循如下规则：

- 本函数将expr中的每一个字节转成两个十六进制字符子串。

- 本函数不支持向量化计算。

- 本函数用作PL内置函数时，其功能有所不同。

**expr**

通用表达式，其值不能为LOB、JSON以及UDT等类型。

- 当expr的数据类型不是RAW时，函数会先将expr（使用一定数量的数据字节表示）转换为具有相同数据字节数的RAW类型数据（数值不做修改）。

- 当expr的值为NULL时，函数返回NULL。

示例（HEAP表）

```sql
SELECT RAWTOHEX(5) res FROM DUAL;
RES
-------------------- 
05

SELECT RAWTOHEX('') res FROM DUAL;
RES
-------------- 
              
SELECT RAWTOHEX('1234abcddfdfh') res FROM DUAL;
RES
---------------------------------------------------------
31323334616263646466646668

SELECT RAWTOHEX(CAST('aa' as clob)) res FROM DUAL;
[1:17]YAS-04401 data type RAW expected, but CLOB got

SELECT RAWTOHEX(CASt('aa' as raw(10))) res from DUAL;
RES
---------
AA
```

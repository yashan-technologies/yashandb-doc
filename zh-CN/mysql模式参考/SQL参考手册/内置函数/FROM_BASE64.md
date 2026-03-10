```ebnf+diagram
from_base64::= FROM_BASE64 "(" expr ")"
```

FROM_BASE64函数返回给定参数[expr](../通用SQL语法/expr)通过BASE-64规则解码后的原始数据。解码可识别并忽略换行、回车、制表符和空格。

**expr**

通用表达式，其值须为二进制类型、字符型或可转换为字符型的其他类型。

- 若expr中第一个等号`=`后出现了除等号以及可忽略字符外的字符，函数返回NULL。
- 若expr剔除可忽略字符后的字节数不是4的倍数，函数返回NULL。
- 若expr中含有非BASE-64编码范围内的字符，函数返回NULL。
- 当expr的值为NULL时，函数返回NULL。

**返回值类型**

- 当expr为字符类型时，返回值为VARCHAR类型。
- 当expr为除BLOB外的二进制类型时，返回值长度若小于65534则为raw类型，否则为blob类型。
- 当expr为BLOB类型时，返回值为blob类型。
- 当expr为CLOB、NCLOB、JSON类型时，返回值为CLOB类型。
- 其余场景返回值均为VARCHAR类型。

示例（HEAP表）

```sql
SELECT from_base64('dGVzdA==') res;

res 
----------------------- 
test                   

```

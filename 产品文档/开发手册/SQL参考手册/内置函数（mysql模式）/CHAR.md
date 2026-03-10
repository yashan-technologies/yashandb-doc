```ebnf+diagram
char::= CHAR "(" expr1 ["," expr2]")"
```

CHAR函数将[expr](../通用SQL语法/expr)表示的一个或多个ASCII码数值转换为对应的字符，返回结果为VARCHAR类型。

**expr**

通用表达式，其值必须为数值型或可以转换为NUMBER类型的字符型。

*   如果expr的值为数值型的小数，函数会先对其四舍五入取整，如果是字符型，则会对其进行向下取整。
*   ASCII码值范围为\[0,255\]，当expr的值小于0时，函数以其补码作为参数，当expr的值大于255时，函数将对其转换为多个字节后再进行转换。
*   expr的值为NULL时，跳过该expr。


示例（单机HEAP表）

```sql
-- 假设expr为字符型数据'42.9'
-- 1. 函数会先将其转为数字进行向下取整，得到42
-- 2. 最后执行ASCII码数值转换
SELECT CHAR('42.9') res FROM DUAL;
RES   
----- 
*   

-- 假设expr为数字类型83,73,67,83
-- 分别将其转为ASCII码对应的字符S、I、C、S
SELECT CHAR(83,73,67,83) res FROM DUAL;
RES
----- 
SICS

```
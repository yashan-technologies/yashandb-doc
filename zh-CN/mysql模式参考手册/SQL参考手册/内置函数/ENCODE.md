```ebnf
encode = ENCODE "(" str "," pass_str")".
```

ENCODE函数使用pass_str作为密码对str进行加密。返回值为VARBINARY类型，长度同str长度一样。

示例（HEAP表）

```sql
SELECT ENCODE('abc', 'value') res FROM DUAL;
res       
--------- 
A77DA3   
```

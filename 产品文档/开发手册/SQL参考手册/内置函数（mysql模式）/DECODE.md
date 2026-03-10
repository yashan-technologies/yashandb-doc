```ebnf+diagram
decode::= DECODE "(" crypt_str "," pass_str ")"
```

DECODE函数使用pass_str作为密码对加密字符串crypt_str进行解密。返回值为VARBINARY类型，长度同crypt_str长度一样。

示例（单机HEAP表）

```sql
SELECT DECODE(ENCODE('abc', 'value'), 'value') res FROM DUAL;
res                                                              
---------------------------------------------------------------- 
616263  
```
```ebnf+diagram
crypt_selftest::= CRYPT_SELFTEST"(" expr1 "," expr2 ")" 
```

CRYPT_SELFTEST函数以[expr1](../通用SQL语法/expr)为测试类型、以[expr2](../通用SQL语法/expr)为数据长度进行条件自测试。

本函数遵循如下规则：

- expr1仅支持random，检查随机数的随机性。

- expr2表示random的比特长度，应为8的整倍数，取值范围为[8,125000]。

示例

```sql
SELECT CRYPT_SELFTEST('random',8) FROM dual;        

CRYPT_SELFTEST('RAND                                             
---------------------------------------------------------------- 
0 Count: 4,  Frequency: 0.50
1 Count: 4,  Frequency: 0.50       

```

```ebnf+diagram
crypt_random::= CRYPT_RANDOM "(" expr1 ")" 
```

CRYPT_RANDOM函数以[expr1](../通用SQL语法/expr)为比特位长度参数，生成指定位数的安全随机数，返回一个HEX格式的VARCHAR类型的随机数序列。

CRYPT_RANDOM会根据指定的位数要求按照字节进行规整后返回生成结果，若位数要求不是整字节数时，对最高字节的高位补0。

本函数遵循如下规则：

- expr1支持计算结果可以转换为整数的类型或表达式，若存在小数部分则会进行截断处理。

- expr1的数值结果的范围需满足[8,125000]的范围要求，超出范围则报错。

示例

```sql
SELECT CRYPT_RANDOM(15) random FROM dual;

RANDOM
---------------------------------------------------------------- 
0E31                                                            


SELECT CRYPT_RANDOM(12 + 34 + 12) random FROM dual;

RANDOM                                                           
---------------------------------------------------------------- 
01CE2D9F46095C56                                                

```

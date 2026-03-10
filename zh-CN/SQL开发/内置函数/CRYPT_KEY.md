```ebnf+diagram
crypt_key::= CRYPT_KEY "(" expr1 ")" 
```

CRYPT_KEY函数以[expr1](../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)为算法类型，创建相应算法的密钥，返回HEX格式的VARCHAR类型的密钥或密钥对。

CRYPT_KEY函数支持创建SM2类型的非对称密钥和SM4的对称密钥。

本函数遵循如下规则：

- expr1只支持SM2和SM4两种算法类型。

- expr1不能为NULL或其他，否则报错。

- expr1为'SM2'时，输出的公私钥对以';'分隔，PUBLIC部分为公钥，PRIVATE部分为私钥。

示例

```sql
SELECT CRYPT_KEY('sm2') FROM dual;

CRYPT_KEY('SM2')                                                 
---------------------------------------------------------------- 
PUBLIC:FFBEBACC2C6A870099B325BEC9128F1CCF544B266B208A5FBEE9ECEFA17C5817A8F4C1F36E0F4CBDB3491BF584E0591F6392AB2337038B4E6DFC04F3903C2299;PRIVATE:EE4714B87EFB48FB0E58A0681E509056609D702C7C64858FF9D8C3E72A7EE65F 


SELECT CRYPT_KEY('sm4') FROM dual;

CRYPT_KEY('SM4')                                                 
---------------------------------------------------------------- 
C970EF5411ADC1DE742A0044EA502B88                                

```

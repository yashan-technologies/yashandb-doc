```ebnf+diagram
DECRYPT_AES128::= DECRYPT_AES128 "(" expr1 "," expr2 ")" 
```

DECRYPT_AES128函数以[expr2](../通用SQL语法/expr)为key解密[expr1](../通用SQL语法/expr)的密文，返回一个VARCHAR类型的明文。

本函数遵循如下规则：

- expr1/expr2只支持VARCHAR/CHAR类型。

- expr2不能为null。

- 当expr1为null时返回null。

> **Caution**:
>
> DECRYPT_AES128函数中的密钥由用户管理，请勿将包含DECRYPT_AES128函数的SQL写入OUTLINE或者SQLMAP视图中，以避免潜在的密钥泄露风险。

示例

```sql
SELECT encrypt_aes128(area_name, 'admin') FROM area;

ENCRYPT_AES128(AREA_
----------------------------------------------------------------
U��G��
U��K��
U��F��
U��F��
U��G��

    
SELECT decrypt_aes128(encrypt_aes128(area_name, 'admin'), 'admin') FROM area;

DECRYPT_AES128(ENCRY
----------------------------------------------------------------
华东
华西
华南
华北
华中

```
```ebnf
ENCRYPT_AES128 = ENCRYPT_AES128 "(" expr1 "," expr2 ")" .
```

ENCRYPT_AES128函数以[expr2](../通用SQL语法/expr)为key加密[expr1](../通用SQL语法/expr)的明文，返回一个VARCHAR类型的密文。

本函数遵循如下规则：

- expr1支持所有可以隐式转换为字符型的类型；expr2只支持VARCHAR/CHAR类型。

- expr2不能为null。

- 当expr1为null时返回null。
> **Caution**:
> 
> ENCRYPT_AES128函数中的密钥由用户管理，请勿将包含ENCRYPT_AES128函数的SQL写入OUTLINE或者SQLMAP视图中，以避免潜在的密钥泄露风险。

示例

```sql
select encrypt_aes128(area_name, 'admin') from area;

ENCRYPT_AES128(AREA_
----------------------------------------------------------------
U��G��
U��K��
U��F��
U��F��
U��G��

5 rows fetched.

```

```ebnf+diagram
DECRYPT_AES128::= DECRYPT_AES128 "(" expr1 "," expr2 ")" 
```

The DECRYPT_AES128 function decrypts the ciphertext of [expr1](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr) using [expr2](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr) as the key and returns a clear text of VARCHAR type.

This function follows these rules:

- expr1/expr2 only support VARCHAR/CHAR types.

- expr2 cannot be null.

- When expr1 is null, it returns null.

> **Caution**:
>
> The key in the DECRYPT_AES128 function is managed by the user. Please do not include SQL containing the DECRYPT_AES128 function in OUTLINE or SQLMAP views to avoid potential key leakage risks.

***Example***

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
EastChina
WestChina
SouthChina
NorthChina
CentralChina

```

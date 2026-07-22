```ebnf
ENCRYPT_AES128 = ENCRYPT_AES128 "(" expr1 "," expr2 ")" .
```

The ENCRYPT_AES128 function encrypts the plaintext [expr1](../General SQL Syntax/expr) using [expr2](../General SQL Syntax/expr) as the key and returns an encrypted value of type VARCHAR.

This function follows these rules:

- expr1 supports all types that can be implicitly converted to character types; expr2 supports only VARCHAR/CHAR types.

- expr2 cannot be null.

- If expr1 is null, the function returns null.
> **Caution**:
> 
> The key used in the ENCRYPT_AES128 function is managed by the user. Do not write SQL containing the ENCRYPT_AES128 function into the OUTLINE or SQLMAP views to avoid potential key leakage risks.

***Example***

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

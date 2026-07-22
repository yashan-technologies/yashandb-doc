```ebnf
crypt_decrypt = CRYPT_DECRYPT"(" expr1 "," expr2 "," expr3 "," expr4 "," expr5 ")" .
```

The CRYPT_DECRYPT function decrypts [expr1](../General SQL Syntax/expr) using [expr2](../General SQL Syntax/expr) as the algorithm type, [expr3](../General SQL Syntax/expr) as the algorithm mode, [expr4](../General SQL Syntax/expr) as the HEX format key, and [expr5](../General SQL Syntax/expr) as the initialization vector, returning the decryption result.

> **Note**:
>
> To use this function with OpenSSL tools, please refer to [Dependency Preparation](../../../Installation and Upgrade/Installation and Deployment/Pre-Installation Preparation/Preparing Dependencies) to check and ensure that the required tools are installed on the server system.

This function follows these rules:

- This function does not support vectorization calculation.

- expr2 only supports SM4.

- expr3 only supports CBC.

- The values of expr4 and expr5 are both in HEX format and must be 32 characters long.

- If expr1 is null, the function returns null.

***Example*** for Heap tables

```sql
select crypt_decrypt('94131D3841F5DEDA81F7651CD7C2CF63', 'sm4', 'CBC', '12345678901234567890123456789012', '12345678901234567890123456789012') as xxxx from dual;

XXXX  
----- 
abc  

1 row fetched.
```

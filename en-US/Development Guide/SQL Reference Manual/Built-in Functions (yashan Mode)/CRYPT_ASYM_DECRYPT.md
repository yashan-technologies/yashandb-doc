```ebnf+diagram
crypt_asym_decrypt::= CRYPT_ASYM_DECRYPT"(" expr1 "," expr2 ")" 
```

The CRYPT_ASYM_DECRYPT function decrypts [expr1](../General SQL Syntax/expr) using [expr2](../General SQL Syntax/expr) as the private key and returns the decryption result.

This function follows the rules below:

- This function does not support vectorization calculation.

- expr1/expr2 only supports VARCHAR/CHAR types.

- When expr1 is null, it returns null.

***Example*** for Heap tables

```sql
SELECT CRYPT_ASYM_DECRYPT('30700220017ABB676F3F36634034D803E89F9154D759C8FED9F610E2C4AD4CD732B8E68F02202BBA7E237F742822BE52DF1C023E0BD73A90CA3FFE8F7E96FA35DCDC1D04BA3B042047C6CC48695184FAC7555101346CB3F5422884B67F7358763D83BD664E31FCED0408D312303A375D0CBF', '69179AA2962473D20BFDC34652E52F145AD03A14CFB026CAB9B2C29127E488BA') FROM dual;

CRYPT_ASYM_DECRYPT(' 
-------------------- 
symEncry            

```

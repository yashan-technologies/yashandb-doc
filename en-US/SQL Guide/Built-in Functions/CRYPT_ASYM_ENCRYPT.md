```ebnf+diagram
crypt_asym_encrypt::= CRYPT_ASYM_ENCRYPT"(" expr1 "," expr2 ")" 
```

The CRYPT_ASYM_ENCRYPT function encrypts [expr1](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr) using the public key [expr2](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr) and returns the encryption result.

This function follows these rules:

- This function does not support vectorization calculation.

- expr1/expr2 only supports VARCHAR/CHAR types.

- If expr1 is null, it returns null.

***Example*** for Heap tables

```sql
SELECT CRYPT_ASYM_ENCRYPT('symEncry', 'A61343A7C65D7BCB87498CBA0453F119568FCE7B9DC8A98E74F5DD68A8A05160E44923350FC5E33D0C91E5E47034235B4478D83564E486494526B414E2CD7EF3') FROM dual;

CRYPT_ASYM_ENCRYPT('                                             
---------------------------------------------------------------- 
30700220017ABB676F3F36634034D803E89F9154D759C8FED9F610E2C4AD4CD732B8E68F02202BBA7E237F742822BE52DF1C023E0BD73A90CA3FFE8F7E96FA35DCDC1D04BA3B042047C6CC48695184FAC7555101346CB3F5422884B67F7358763D83BD664E31FCED0408D312303A375D0CBF

```

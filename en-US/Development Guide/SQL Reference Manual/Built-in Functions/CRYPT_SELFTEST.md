```ebnf
crypt_selftest = CRYPT_SELFTEST"(" expr1 "," expr2 ")" .
```

The CRYPT_SELFTEST function performs a self-test based on [expr1](../General SQL Syntax/expr) as the test type and [expr2](../General SQL Syntax/expr) as the data length.

This function follows these rules:

- expr1 only supports random, which checks the randomness of the random numbers.

- expr2 represents the bit length of random, which must be a multiple of 8 with a value range of [8,125000].

***Example***

```sql
select crypt_selftest('random',8) from dual;        

CRYPT_SELFTEST('RAND                                             
---------------------------------------------------------------- 
0 Count: 4,  Frequency: 0.50
1 Count: 4,  Frequency: 0.50       

1 row fetched.
```

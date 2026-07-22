```ebnf
crypt_random = CRYPT_RANDOM "(" expr1 ")" .
```

The CRYPT_RANDOM function generates a secure random number of a specified bit length using [expr1](../General SQL Syntax/expr) as the parameter for the bit length, and returns a random number sequence in HEX format as a VARCHAR type.

CRYPT_RANDOM will return the generated result aligned to bytes according to the specified bit requirement. If the bit requirement is not an integer multiple of a byte, the high bits of the highest byte will be padded with 0.

This function follows these rules:

- expr1 supports types or expressions whose computed result can be converted to an integer. If there is a decimal part, it will be truncated.

- The numeric result of expr1 must meet the range requirement of [8,125000]; exceeding this range will result in an error.

***Example***

```sql
select crypt_random(15) random from dual;

RANDOM
---------------------------------------------------------------- 
0E31                                                            

1 row fetched.

select crypt_random(12 + 34 + 12) random from dual;

RANDOM                                                           
---------------------------------------------------------------- 
01CE2D9F46095C56                                                

1 row fetched.
```

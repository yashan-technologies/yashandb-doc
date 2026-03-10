```ebnf+diagram
oct::= OCT "(" (str|N) ")"
```

The OCT function will perform octal conversion on the input string or number. 

This function follows the rules below:

- It will throw an error or forcibly convert data that cannot be directly represented as BIGINT into BIGINT for uniform processing.
- During the conversion of a string to a number, illegal characters will be truncated, and scientific notation is not supported.
- Treat N as BIGINT (decimal rounding). The effective upper bound is 2^64-1 and the effective lower bound is -2^63. If it exceeds the upper bound, output the corresponding binary all as 1s; if it exceeds the lower bound, output the corresponding binary as all 0s except for the sign bit. Positive numbers will output the octal of the corresponding binary unsigned integer, and negative numbers will output the octal of the corresponding binary in two's complement.
- If the input parameter is NULL, the function will return NULL.

***Example*** for  Heap tables

```sql

SELECT OCT(8) res;

res
------
10


SELECT OCT('12a') res;

res
----------
14

```

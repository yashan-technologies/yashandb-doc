```ebnf
hex = HEX "(" (str|N) ")".
```

The HEX function converts the input string or number to its hexadecimal representation. 

This function follows the rules below:

- An error is raised for data that cannot be directly represented as bigint, or it is forcefully converted to bigint for uniform processing.
- During the conversion of a string to a number, illegal characters will be truncated, and scientific notation is not supported.
- N is treated as BIGINT (decimal rounded). The effective upper limit is 2^64-1, and the effective lower limit is -2^63. If the upper limit is exceeded, it outputs the corresponding binary as all 1s. If the lower limit is exceeded, it outputs the corresponding binary as all 0s except for the sign bit. Positive numbers output the unsigned integer's corresponding binary in hexadecimal; negative numbers output the two's complement binary in hexadecimal.
- If the input is NULL, the function returns NULL.

***Example*** for Standalone Deployment Heap tables

```sql

select hex('YASHAN') res;

res
-------------
59415348414E

```

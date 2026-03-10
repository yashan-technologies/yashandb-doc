```ebnf+diagram
split::= SPLIT "(" expr "," delimiter "," n ")"
```

The SPLIT function divides the string represented by [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr) into several substrings based on the specified delimiter, returning the nth substring.

**expr**

A generic expression whose value must be character type or another type that can be converted to character, excluding JSON, LOB, and BFILE types.

- When the value of expr is of type CHAR, trailing spaces will be removed from the string.

- When the value of expr is NULL, the function returns NULL.

**delimiter**

A delimiter that is the same generic expression as expr, must be character type or another type that can be converted to character, excluding JSON, LOB, and BFILE types.

- When the value of delimiter is of type CHAR, trailing spaces will be removed from the string, and if it is a string of all spaces (e.g. `'   '`), it will ultimately be treated as one space (i.e. `' '`).

- When the value of delimiter is NULL, the function returns NULL.

**n**

Specifies the sequence number of the substring to return, n must be a numeric type or character type data that can be converted to NUMBER, with a value range of [-2147483648,0)∪(0,2147483647]. Positive values indicate order from left to right, while negative values indicate order from right to left.

- When n is 0, the function will raise an error.

- When n is a decimal, the decimal part will be truncated, keeping only the integer part.

- When the absolute value of n is greater than the number of substrings produced by the split, the function returns NULL.

- When the value of n is NULL, the function returns NULL.

***Example***

```sql
SELECT SPLIT('a,b,c', ',', 1) a
,SPLIT('a,b,c', ',', 4) b
,SPLIT('Guangdong——Shenzhen', '——', 1) c
,SPLIT('a,b,c', ',' , 2.9) d
FROM DUAL;
A     B     C             D
----- ----- ------------- -----
a           Guangdong     b
```

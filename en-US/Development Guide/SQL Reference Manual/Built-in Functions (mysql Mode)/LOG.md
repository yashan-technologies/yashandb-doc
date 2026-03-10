```ebnf+diagram
log::= LOG "(" expr1 ["," expr2] ")"
```
The LOG function is synonymous with the [LN](LN) function when it has one argument.

When the LOG function has two arguments, it calculates the logarithm of expr2 to the base of expr1, returning a value of type DOUBLE.

Both expr1 and expr2 are general expressions recognized by YashanDB, and their values must be numeric or convertible to DOUBLE type characters. Other types are not supported by the function's return type.

If either expr1 or expr2 is NULL, the function returns NULL.

According to the mathematical concept of logarithms, expr1 should be a positive number excluding 0 and 1, and expr2 should be any positive number. The function handles other cases as shown in the following table:

| expr1       | LOG(expr1, expr2)              |
|-------------|--------------------------------|
| Negative, -Inf, 0, 1 | Invalid argument for logarithm |
| Inf         | Inf                            |
| Nan, -Nan    | Nan                            |

| expr2     | LOG(expr1, expr2)              |
|-----------|--------------------------------|
| Negative, -Inf, 0 | Invalid argument for logarithm |
| Inf       | Inf                            |
| Nan, -Nan  | Nan                            |

***Example*** for Standalone Deployment Heap tables

```sql
SELECT LOG(2,4) res;
        RES
-----------
   2.0E+000

SELECT LOG(b'10',b'100');
        RES
-----------
   2.0E+000
SELECT LOG(2) res;
        RES
-----------
 6.931E-001
```

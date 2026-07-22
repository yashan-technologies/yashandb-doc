```ebnf
pi = PI  "("")" .
```

The PI function has no given parameters and returns the value of pi, with the return data type being DOUBLE.

***Example***

```sql
-- Get the value of pi
SELECT PI() from DUAL;

                 PI()
---------------------
 3.1415926535898E+000
```

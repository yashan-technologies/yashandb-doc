```ebnf+diagram
pi::= PI  "("")" 
```

The PI function returns the value of pi without any parameters, and the return data type is DOUBLE.

***Example*** for Standalone Deployment Heap tables

```sql
-- Get the value of pi
SELECT PI() FROM DUAL;

                 PI()
---------------------
 3.1415926535898E+000
```

```ebnf+diagram
format::= FORMAT "(" X "," D ")"
```

The FORMAT function formats the number X as "#,###,###.###", rounding to D decimal places, and returns the result as a string. If D is 0, the result will have no decimal point or decimal part.

This function follows these rules:

- If X or D is NULL, the function returns NULL.
- If X is a non-numeric type, the function first converts it to a numeric type, recognizing it in scientific notation; if it is not in scientific notation, it will be truncated immediately.
- If D is a non-numeric type, the function first converts it to a numeric type, which cannot be recognized in scientific notation, and valid characters include only '0'-'9' and a '-' as a sign; any other characters will be truncated directly.

***Example*** for Standalone Deployment Heap tables

```sql

SELECT FORMAT('1e2',1);

format('1e2',1) 
--------------- 
100.0          

```

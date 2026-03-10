```ebnf+diagram
sign::= SIGN "(" expr ")"
```

The SIGN function returns the sign of the value represented by [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr), which includes 1, -1, and 0.

The value of expr must be numeric or a character type that can be converted to NUMBER type (a failed conversion returns an Invalid number error). For other types, the function returns an unsupported type.

When the value of expr is NULL, the function returns NULL.

When the value of expr is a positive number, the function returns 1; when the value of expr is a negative number, the function returns -1; when the value of expr is 0, the function returns 0.

For special values in floating-point data such as Nan, -Inf, and Inf:

- SIGN(Nan) = 1
- SIGN(Inf) = 1
- SIGN(-Inf) = -1

***Example***

```sql
DROP TABLE IF EXISTS number_fd;
CREATE TABLE number_fd(numberf FLOAT, numberd DOUBLE);
INSERT INTO number_fd VALUES('NaN','inf');
INSERT INTO number_fd VALUES('',5.5333333323);
INSERT INTO number_fd VALUES('-inf','-4.322323');
COMMIT;
 
SELECT SIGN(numberf) sign1, SIGN(numberd) sign2 FROM number_fd;
      SIGN1       SIGN2
----------- -----------
          1           1
                      1
         -1          -1
```

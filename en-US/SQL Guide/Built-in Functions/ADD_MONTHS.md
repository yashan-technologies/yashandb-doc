```ebnf+diagram
add_months::= ADD_MONTHS "(" date_expr "," integer_expr ")"
```

The ADD_MONTHS function calculates the date represented by date_expr plus the number of months represented by integer_expr (which can be negative), returning a DATE type value.

**date_expr**

The value of date_expr must be of DATE, TIMESTAMP, or character data type, as recognized by YashanDB's [general expression](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr).

- When date_expr is of character type, it must ensure that the string complies with the current DATE type format requirements; otherwise, an error is returned.

- When date_expr is NULL, the function returns NULL.

**integer_expr**

The value of integer_expr must be of NUMBER type or convertible to NUMBER character type, as recognized by YashanDB's [general expression](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr).

When integer_expr has a decimal value:

- For floating-point numbers (FLOAT and DOUBLE), round to the nearest integer using even rounding.
- For non-floating-point numbers, directly truncate the decimal, leaving the integer part.

**Special Handling of Days**

Due to the occurrence of months with varying lengths, special handling is applied to days:

* When the DAY value of date_expr is the last day of the month, the function returns a DAY value that is also the last day of its month.
* When the DAY value of date_expr exceeds the last day of the resulting month after adding integer_expr, the function returns a DAY value set to the last day of that month.

***Example***

```sql
SELECT ADD_MONTHS(DATE'2021-5-31',-3) res FROM DUAL;
RES
--------------------------------
2021-02-28

SELECT ADD_MONTHS(DATE'2022-1-30',1) res FROM DUAL;
RES
--------------------------------
2022-02-28
```

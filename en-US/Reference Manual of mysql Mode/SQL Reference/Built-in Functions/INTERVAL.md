```ebnf
interval = INTERVAL "(" expr ", " expr0 ", " expr1 ", " expr2 ", ..." ")".
```

The comparison function INTERVAL is used to find the sorting position of the first parameter in the subsequent list of n parameters. This function compares the first expr sequentially with the subsequent n exprs, and returns the corresponding position index when the first value greater than expr is found. For example, when expr0 is greater than expr, it returns 0; when expr1 is greater than expr, it returns 1. If there is no value greater than the first expr in the list, it returns n+1.


- The number of input parameters for this function must be between 2-65535.

- When this function performs numeric comparison, it will convert the input parameter data types according to the table below before performing the comparison.

    |Input Data Type|Converted Data Type|
    |-----------|-------------|
    |BOOLEAN/TINYINT/TINYINT UNSIGNED/<br>SMALLINT/SMALLINT UNSIGNED/INT/DATE|INTEGER|
    |BIGINT/TIMESTAMP(precision 0)|BIGINT|
    |BIGINT UNSIGNED/INT UNSIGNED|BIGINT UNSIGNED|
    |NUMBER/TIMESTAMP(precision not 0)|NUMBER|
    |FLOAT/DOUBLE/CHAR/VARCHAR|DOUBLE|
    |TEXT/BLOB|NUMBER|

- When the first expr is null, -1 is returned. When there are null values among the subsequent n exprs, the next expr is selected for comparison.

- Input parameters of non-pure numerical character types and BINARY types are not currently supported.

***Example*** for Standalone Deployment Heap tables

```sql
SQL> SELECT INTERVAL(10, 5.2, 9.8, 10.1, 15, 20);

INTERVAL(10, 5.2, 9.8, 10.1, 15, 20)
------------------------------------
                                   2

1 row fetched.

SQL> SELECT INTERVAL(TRUE, FALSE, TRUE, FALSE);

INTERVAL(TRUE, FALSE, TRUE, FALSE)
----------------------------------
                                 3

1 row fetched.

SQL> SELECT INTERVAL('10', '5', '10', '15', '20');

INTERVAL('10', '5', '10', '15', '20')
-------------------------------------
                                    2

1 row fetched.

SQL> SELECT INTERVAL(NULL, 5, 10, 15);

INTERVAL(NULL, 5, 10, 15)
-------------------------
                       -1

1 row fetched.

SQL> SELECT INTERVAL(10, 5, NULL, NULL, NULL, NULL, 15, 20);

INTERVAL(10, 5, NULL, NULL, NULL, NULL, 15, 20)
-----------------------------------------------
                                              5

1 row fetched.

SQL> SELECT INTERVAL('10abc', '5.2', '9.8' '10.1', '15', '20');

[1:39]YAS-04209 unexpected word '10.1'

SQL> SELECT INTERVAL(1);

[1:8]YAS-04309 arguments count must be between 2 and 65535
```

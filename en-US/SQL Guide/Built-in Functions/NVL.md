```ebnf+diagram
nvl::= NVL "(" expr1 "," expr2 ")"
```

The NVL function evaluates from left to right and returns the value of the first non-null expression among the two [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr) parameters.

If both expr1 and expr2 are NULL, the function returns NULL.

Both expr1 and expr2 support implicit conversion of LOB types.

When the data types of expr1 and expr2 are different, the function first performs type conversion. If the two data types cannot be converted according to certain rules, it will return a type conversion error. The type conversion rules are as follows:

* If expr1 is of character type, expr2 will be converted to VARCHAR type, and the return value will be of VARCHAR type.
* If both expr1 and expr2 are numeric types, the function will determine the data type with the highest precision among all data and convert the execution results of all expr to that type, with the return value also being of that type.
* If both expr1 and expr2 are of DATE, TIMESTAMP, or timezone types, the return type priority is TIMESTAMP TZ -> TIMESTAMP LTZ -> TIMESTAMP -> DATE.
* If expr1 is of unknown type, expr1 will be converted to the type of expr2.
* If expr2 is of unknown type, expr2 will be converted to the type of expr1.
* If both expr1 and expr2 are of unknown types, both will be converted to VARCHAR type.
* If the types of expr1 and expr2 are known and different while not satisfying situation 1 or situation 2 above, expr2 will be forcibly converted to the data type of expr1. If the conversion condition is not met, it will return an invalid datatype error; specific type conversion rules are described in the [CAST](CAST) function.

***Example***

```sql
SELECT NVL(NOW(),'2011-05-11 12:28:04') res FROM DUAL;
RES                               
--------------------------------
2021-12-05 18:02:48                     
 
SELECT NVL('a',1) res FROM DUAL;
RES    
-----
a
```

```ebnf+diagram
if::= IF "(" expr1 "," expr2 "," expr3 ")"
```

The IF function has 3 [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr) parameters. If expr1 is TRUE, it returns expr2; if expr1 is FALSE, it returns expr3.

The value of expr1 can be of any data type. If it is NULL, it is considered FALSE. If it is not NULL:

* For numeric types or other types that can be implicitly converted to numeric types, a value that is non-zero is considered TRUE, while a value of zero is considered FALSE;
* For types that cannot be implicitly converted to numeric types, it is considered FALSE;
* For BOOLEAN types and BOOLEAN expressions, it is handled according to its truth value.

The values of expr2 and expr3 can also be of any data type. When the data types of expr2 and expr3 are different, the function will perform implicit type conversion before returning the result. The conversion rules are consistent with those in [IFNULL](IFNULL).

It is important to note that this function has the following specifications that differ from the IF function in MySQL:

* When both expr2 and expr3 are of BIT type, YashanDB returns BIT type, while MySQL returns INT/BIGINT UNSIGNED type;
* When both expr2 and expr3 are of BOOLEAN, TINYINT, or SMALLINT types, YashanDB performs type conversion according to the priority of BOOLEAN < TINYINT < SMALLINT, while MySQL returns all as INT type;

***Example***

```sql
SELECT IF(1, 2, 3) res FROM DUAL;
         RES 
------------ 
           2

SELECT IF(0, 2, 3) res FROM DUAL;
         RES 
------------ 
           3

SELECT IF(SYSDATE, TRUE, FALSE) res FROM DUAL;
RES                  
-------------------- 
false    

SELECT IF(TRUE, TRUE, FALSE) res FROM DUAL;
RES                  
-------------------- 
true 

SELECT IF(FALSE, TRUE, FALSE) res FROM DUAL;
RES                  
-------------------- 
false   

SELECT IF(2,5.44, CAST('0.232222' AS DOUBLE)) res,
TYPEOF (IF(2,5.44, CAST('0.232222' AS DOUBLE))) res_type
FROM DUAL;
        RES RES_TYPE                                                         
----------- ----------- 
  5.44E+000 double       
```

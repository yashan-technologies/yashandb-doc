```ebnf+diagram
pow::= POW "(" expr "," exp ")"
power::= POWER "(" expr "," exp ")"
```

The POW/POWER function calculates the *exp* power of the parameter value of [expr](../General SQL Syntax/expr), and its return type is as follows:

- When the value of expr is of type TINYINT, SMALLINT, INT, BIGINT, NUMBER, CHAR, VARCHAR, NCHAR, or NVARCHAR, it returns NUMBER.
- When the value of expr is of type FLOAT or DOUBLE, it returns DOUBLE.
- When the value of *exp* is of type FLOAT or DOUBLE, it returns DOUBLE.
- When either expr or *exp* has a value of NULL, the function returns NULL.
- An error will occur for expr or *exp* that cannot be converted to NUMBER type.

**exp**

Exponent.

*exp* is a general expression that is the same as expr; when *exp* has a value of NULL, the function returns NULL. 

The following table shows the calculation rules of this function under different circumstances (character types will be implicitly converted to NUMBER type in participating in the rules of the table):

|expr Base Data Type |expr Base Value |exp Exponent Data Type |exp Exponent Value |Function Calculation Result |
| ------------------ | ----------- | --------------------------- | --------- | ------------- |
| Integer/Float/NUMBER | Positive/Negative/0 | Integer/Float/NUMBER        | 0                   | 1                           |
| Integer/Float/NUMBER | Positive        | Integer/Float/NUMBER        | Positive/Negative    | expr's *exp* power         |
| Integer/Float/NUMBER | 0               | Integer/Float/NUMBER        | Positive            | 0                           |
| Integer/Float/NUMBER | 0               | Float                      | Negative            | Inf                         |
| Integer/Float/NUMBER | 0               | Integer/NUMBER             | Negative            | YAS-00012 error            |
| Integer              | Negative        | Integer/NUMBER (no decimal) | Positive/Negative    | expr's *exp* power         |
| Integer              | Negative        | Float                      | Positive/Negative    | Nan                         |
| Integer              | Negative        | NUMBER (with decimal)      | Positive/Negative    | YAS-04426 error            |
| Float                | Negative        | Integer                    | Positive/Negative    | expr's *exp* power         |
| Float                | Negative        | Float                      | Positive/Negative    | Nan                         |
| Float                | Negative        | NUMBER                     | Positive/Negative    | Nan                         |
| NUMBER               | Negative        | Integer/NUMBER (no decimal) | Positive/Negative    | expr's *exp* power         |
| NUMBER               | Negative        | Float                      | Positive/Negative    | Nan                         |
| NUMBER               | Negative        | NUMBER (with decimal)      | Positive/Negative    | YAS-04426 error            |

***Example***

```sql
SET NUMWIDTH 20;

SELECT POW(2,2) res FROM DUAL;
                  RES 
--------------------- 
                    4

SELECT POW(2,-2) res FROM DUAL;
                  RES 
--------------------- 
                  .25

SELECT POW(2,2.1) res FROM DUAL;
                  RES
---------------------
 4.287093850145172657

SELECT POWER(2.2,-12.1) res FROM DUAL;
                  RES
---------------------
 .0000718928270982645
           
SELECT POW(-1,1.1) res FROM DUAL;
[1:13]YAS-04426 the argument value is out of range

SELECT POW(0, -1) res FROM DUAL;
[1:12]YAS-00012 numeric overflow
```

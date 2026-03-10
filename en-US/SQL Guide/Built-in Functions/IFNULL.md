```ebnf+diagram
ifnull::= IFNULL "(" expr1 "," expr2 ")"
```

The IFNULL function has 2 [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr) parameters. It returns expr1 when expr1 is not NULL; otherwise, it returns expr2.

The values of expr1 and expr2 can be of any data type, but if either expr1 or expr2 is of type RAW, JSON, CLOB, NCLOB, BLOB, or BFILE, they are not allowed to be used with other types (i.e., expr1 and expr2 must have the same data type); otherwise, the function will return an error.

The return value type rules for the function are as follows:

- If either expr1 or expr2 is NULL and the other is a constant, the return value type of the function will be the data type corresponding to the constant.

- If expr1 and expr2 have the same data type, the function returns the value of that data type.

- If expr1 and expr2 have different data types, the function will perform implicit type conversion before returning the result. The basic rules are as follows:

  - If expr1 and expr2 belong to different main groups of numeric types (except BIT), date-time types, character types, or ROWID:

    - If they do not include NCHAR or NVARCHAR, the function returns a value of VARCHAR type.

    - If they include NCHAR or NVARCHAR, the function returns a value of NVARCHAR type.

  - If either expr1 or expr2 is of BOOLEAN type:

    - If the other belongs to a non-numeric category, the function returns a value of VARCHAR type.

    - If the other belongs to a numeric type (except BIT), the function returns the corresponding numeric type.

    - If the other is of BIT type, the function returns BIGINT type.

  - If either expr1 or expr2 is of BIT type:

    - If the other belongs to a non-numeric category, the function returns a value of VARCHAR type.

    - If the other belongs to a numeric type:

      - For INTEGER types, the function returns BIGINT type.

      - For FLOAT types, the function returns DOUBLE type.

      - For NUMBER types, the function returns NUMBER type.

      - For BIT types, the function returns BIT type.

  - If expr1 and expr2 are both of date-time types, the return type is shown in the following table (when converting TIME types to DATE or TIMESTAMP types, the year, month, and day will be supplemented with the current date):

| expr1/expr2                          | DATE      | TIME      | TIMESTAMP | INTERVAL<br/> YEAR TO <br/>MONTH | INTERVAL <br/>DAY TO <br/>SECOND |
| ------------------------------------ | --------- | --------- | --------- | -------------------------------- | -------------------------------- |
| **DATE**                             | DATE      | DATE      | TIMESTAMP | VARCHAR                          | VARCHAR                          |
| **TIME**                             | DATE      | TIME      | TIMESTAMP | VARCHAR                          | VARCHAR                          |
| **TIMESTAMP**                        | TIMESTAMP | TIMESTAMP | TIMESTAMP | VARCHAR                          | VARCHAR                          |
| **INTERVAL <br/>YEAR TO <br/>MONTH** | VARCHAR   | VARCHAR   | VARCHAR   | INTERVAL <br/>YEAR TO <br/>MONTH | VARCHAR                          |
| **INTERVAL <br/>DAY TO <br/>SECOND** | VARCHAR   | VARCHAR   | VARCHAR   | VARCHAR                          | INTERVAL <br/>DAY TO <br/>SECOND |

  - If expr1 and expr2 are both of numeric types, the return type is shown in the following table (only when expr1 and expr2 are both of NUMBER type and have the same precision will the function return a NUMBER type of the same precision):

| expr1/expr2  | BIT    | TINYINT  | SMALLINT | INT    | BIGINT | FLOAT  | DOUBLE | NUMBER      |
| ------------ | ------ | -------- | -------- | ------ | ------ | ------ | ------ | ----------- |
| **BIT**      | BIT    | BIGINT   | BIGINT   | BIGINT | BIGINT | DOUBLE | DOUBLE | NUMBER      |
| **TINYINT**  | BIGINT | TINYINT  | SMALLINT | INT    | BIGINT | FLOAT  | DOUBLE | NUMBER      |
| **SMALLINT** | BIGINT | SMALLINT | SMALLINT | INT    | BIGINT | FLOAT  | DOUBLE | NUMBER      |
| **INT**      | BIGINT | INT      | INT      | INT    | BIGINT | FLOAT  | DOUBLE | NUMBER      |
| **BIGINT**   | BIGINT | BIGINT   | BIGINT   | BIGINT | BIGINT | FLOAT  | DOUBLE | NUMBER      |
| **FLOAT**    | DOUBLE | FLOAT    | FLOAT    | FLOAT  | FLOAT  | FLOAT  | DOUBLE | DOUBLE      |
| **DOUBLE**   | DOUBLE | DOUBLE   | DOUBLE   | DOUBLE | DOUBLE | DOUBLE | DOUBLE | DOUBLE      |
| **NUMBER**   | floating point NUMBER | floating point NUMBER | floating point NUMBER | floating point NUMBER | floating point NUMBER | floating point NUMBER | floating point NUMBER | floating point NUMBER or NUMBER(p/s) |

***Example***

```sql
SELECT IFNULL(1, 2) res FROM DUAL;
         RES 
------------ 
           1

SELECT IFNULL(NULL, 2) res FROM DUAL;
RES   
----- 
2    

SELECT IFNULL(TRUE, 2) res FROM DUAL;
         RES 
------------ 
           1
             
SELECT IFNULL(5, CAST('0.232222' AS FLOAT)) res,
TYPEOF (IFNULL(5, CAST('0.232222' AS FLOAT))) res_type
FROM DUAL;
        RES RES_TYPE                                                         
----------- ---------- 
   5.0E+000 float 
```

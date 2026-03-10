```ebnf+diagram
ifnull::= IFNULL "(" expr1 "," expr2 ")"
```

The IFNULL function has 2 [expr](../General SQL Syntax/expr) parameters. It returns expr1 when expr1 is not NULL; otherwise, it returns expr2.

The values of expr1 and expr2 can be of any data type. However, if either expr1 or expr2 is of type RAW, JSON, CLOB, NCLOB, or BLOB, it is not allowed to be used in combination with other types (i.e., the data types of expr1 and expr2 must be the same); otherwise, the function returns an error.

The return value type rules for the function are as follows:

- When one of expr1 or expr2 is NULL and the other is a constant, the function returns the data type corresponding to the constant.

- When expr1 and expr2 have the same data type, the function returns a value of this data type.

- When expr1 and expr2 have different data types, the function will first perform implicit type conversion before returning the result. The basic rules are as follows:

  - If expr1 and expr2 belong to different major categories of numeric types (excluding BIT), date-time types, character types, or ROWID:

    - If neither contains NCHAR or NVARCHAR, the function returns a value of type VARCHAR.

    - If either contains NCHAR or NVARCHAR, the function returns a value of type NVARCHAR.

  - If either expr1 or expr2 is of type BOOLEAN:

    - If the other belongs to a non-numeric major category, the function returns a value of type VARCHAR.

    - If the other belongs to a numeric type (excluding BIT), the function returns the corresponding numeric type.

    - If the other is of type BIT, the function returns a value of type BIGINT.

  - If either expr1 or expr2 is of type BIT:

    - If the other belongs to a non-numeric major category, the function returns a value of type VARCHAR.

    - If the other belongs to a numeric type:

      - If it is an integer type, the function returns a value of type BIGINT.

      - If it is a floating-point type, the function returns a value of type DOUBLE.

      - If it is of type NUMBER, the function returns a value of type NUMBER.

      - If it is of type BIT, the function returns a value of type BIT.

  - If both expr1 and expr2 are of date-time type, the function returns the types as shown in the table below (when converting TIME types to DATE or TIMESTAMP types, the year, month, and day will be supplemented with the current date):

| expr1/expr2                          | DATE      | TIME      | TIMESTAMP | INTERVAL<br/> YEAR TO <br/>MONTH | INTERVAL <br/>DAY TO <br/>SECOND |
| ------------------------------------ | --------- | --------- | --------- | -------------------------------- | -------------------------------- |
| **DATE**                             | DATE      | DATE      | TIMESTAMP | VARCHAR                          | VARCHAR                          |
| **TIME**                             | DATE      | TIME      | TIMESTAMP | VARCHAR                          | VARCHAR                          |
| **TIMESTAMP**                        | TIMESTAMP | TIMESTAMP | TIMESTAMP | VARCHAR                          | VARCHAR                          |
| **INTERVAL <br/>YEAR TO <br/>MONTH** | VARCHAR   | VARCHAR   | VARCHAR   | INTERVAL <br/>YEAR TO <br/>MONTH | VARCHAR                          |
| **INTERVAL <br/>DAY TO <br/>SECOND** | VARCHAR   | VARCHAR   | VARCHAR   | VARCHAR                          | INTERVAL <br/>DAY TO <br/>SECOND |

  - If both expr1 and expr2 are of numeric type, the function returns the types as shown in the table below (the function returns the same precision NUMBER type only when both expr1 and expr2 are of type NUMBER and have the same precision):

| expr1/expr2           | BIT             | TINYINT          | SMALLINT | INT              | BIGINT | FLOAT  | DOUBLE | NUMBER      | TINYINT UNSIGNED | SMALLINT UNSIGNED| INT UNSIGNED     | BIGINT UNSIGNED |
|-----------------------|-----------------|------------------| -------- |------------------| ------ | ------ | ------ | ----------- |------------------| ------ |------------------|-----------------|
| **BIT**               | BIT             | BIGINT           | BIGINT   | BIGINT           | BIGINT | DOUBLE | DOUBLE | NUMBER      | BIGINT UNSIGNED  | BIGINT UNSIGNED| BIGINT UNSIGNED  | BIGINT UNSIGNED |
| **TINYINT**           | BIGINT          | TINYINT          | SMALLINT | INT              | BIGINT | FLOAT  | DOUBLE | NUMBER      | BIGINT UNSIGNED  | BIGINT UNSIGNED| BIGINT UNSIGNED  | BIGINT UNSIGNED |
| **SMALLINT**          | BIGINT          | SMALLINT         | SMALLINT | INT              | BIGINT | FLOAT  | DOUBLE | NUMBER      | BIGINT UNSIGNED  | BIGINT UNSIGNED| BIGINT UNSIGNED  | BIGINT UNSIGNED |
| **INT**               | BIGINT          | INT              | INT      | INT              | BIGINT | FLOAT  | DOUBLE | NUMBER      | BIGINT UNSIGNED  | BIGINT UNSIGNED| BIGINT UNSIGNED  | BIGINT UNSIGNED |
| **BIGINT**            | BIGINT          | BIGINT           | BIGINT   | BIGINT           | BIGINT | FLOAT  | DOUBLE | NUMBER      | BIGINT UNSIGNED  | BIGINT UNSIGNED| BIGINT UNSIGNED  | BIGINT UNSIGNED |
| **FLOAT**             | DOUBLE          | FLOAT            | FLOAT    | FLOAT            | FLOAT  | FLOAT  | DOUBLE | DOUBLE      | FLOAT            | FLOAT    | FLOAT            | FLOAT           |
| **DOUBLE**            | DOUBLE          | DOUBLE           | DOUBLE   | DOUBLE           | DOUBLE | DOUBLE | DOUBLE | DOUBLE      | DOUBLE          | DOUBLE           | DOUBLE   | DOUBLE          |      
| **NUMBER**            | Floating-point NUMBER | Floating-point NUMBER | Floating-point NUMBER | Floating-point NUMBER | Floating-point NUMBER | Floating-point NUMBER | Floating-point NUMBER | Floating-point NUMBER or NUMBER(p/s) | Floating-point NUMBER | Floating-point NUMBER | Floating-point NUMBER | Floating-point NUMBER |
| **TINYINT UNSIGNED**  | BIGINT UNSIGNED | BIGINT UNSIGNED  | BIGINT UNSIGNED| BIGINT UNSIGNED  | BIGINT UNSIGNED| FLOAT  | DOUBLE | NUMBER      | TINYINT UNSIGNED  | BIGINT UNSIGNED| BIGINT UNSIGNED  | BIGINT UNSIGNED |
| **SMALLINT UNSIGNED** | BIGINT UNSIGNED | BIGINT UNSIGNED  | BIGINT UNSIGNED| BIGINT UNSIGNED  | BIGINT UNSIGNED| FLOAT  | DOUBLE | NUMBER      | BIGINT UNSIGNED  | SMALLINT UNSIGNED| BIGINT UNSIGNED  | BIGINT UNSIGNED |
| **INT UNSIGNED**      | BIGINT UNSIGNED | BIGINT UNSIGNED  | BIGINT UNSIGNED| BIGINT UNSIGNED  | BIGINT UNSIGNED| FLOAT  | DOUBLE | NUMBER      | BIGINT UNSIGNED  | BIGINT UNSIGNED| INT UNSIGNED  | BIGINT UNSIGNED |
| **BIGINT UNSIGNED**   | BIGINT UNSIGNED | BIGINT UNSIGNED  | BIGINT UNSIGNED| BIGINT UNSIGNED  | BIGINT UNSIGNED| FLOAT  | DOUBLE | NUMBER      | BIGINT UNSIGNED  | BIGINT UNSIGNED| BIGINT UNSIGNED  | BIGINT UNSIGNED |

***Example*** for Standalone Deployment Heap tables

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
             
SELECT IFNULL(5, CAST('0.232222' AS NUMBER)) res,
TYPEOF (IFNULL(5, CAST('0.232222' AS NUMBER))) res_type
FROM DUAL;
        RES RES_TYPE                                                         
----------- -----------------
          5 number
```

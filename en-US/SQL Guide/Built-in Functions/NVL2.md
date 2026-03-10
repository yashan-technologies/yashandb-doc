```ebnf+diagram
nvl2::= NVL2 "(" expr1 "," expr2 "," expr3 ")"
```

The NVL2 function is used to determine whether to return the value of expr2 or expr3 based on whether expr1 is null.

expr1, expr2, and expr3 are all [general expressions](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr) recognized by YashanDB.

expr1, expr2, and expr3 all support implicit conversion for LOB and BFILE types.

The rules for the function return value are as follows:

* If expr1 is not null, the value of expr2 is returned.
* If expr1 is null, the value of expr3 is returned.

The rules for the data type of the function return value are as follows:

* If either expr2 or expr3 is null, the data type of the function return value is consistent with the non-null value.
* If both expr2 and expr3 are null, the data type of the function return value is VARCHAR.
* If both expr2 and expr3 are not null and their data types are different, the function will first perform [data type conversion](../../All Manuals/Development Guide/SQL Reference Manual/Data Types (yashan Mode)/Data Type Conversion) and return the converted data type; if the two data types cannot be converted, a type conversion error will be returned. When both data types support conversion, the conversion rules are as follows:
    * If both expr2 and expr3 are numeric types, the function will convert the data type with the lower priority to the data type with the higher priority according to the promotion rules (TINYINT -> SMALLINT -> INT -> BIGINT -> NUMBER -> FLOAT -> DOUBLE).
    * If both expr2 and expr3 are DATE, TIMESTAMP, or timezone types, the return type priority is TIMESTAMP TZ -> TIMESTAMP LTZ -> TIMESTAMP -> DATE.
    * If expr2 is of another type, the function will implicitly convert expr3 to the data type of expr2.

***Example***

```sql
-- Because the first parameter is not null, it returns the second parameter, while implicit type conversion is performed between the second and third parameters
SELECT NVL2(1,NOW(),'2011-04-01 12:28:03') res FROM DUAL;
RES                              
-------------------------------- 
2022-10-09 00:17:09  

-- Because the first parameter is null, it returns the third parameter, while implicit type conversion is performed between the second and third parameters
SELECT NVL2('',NOW(),'2011-04-01 12:28:03') res FROM DUAL;
RES                              
-------------------------------- 
2011-04-01 12:28:03 

-- Implicit type conversion fails
SELECT NVL2(null,NOW(),'an apple') res FROM DUAL;
[1:13]YAS-00008 type convert error : literal does not match format string

-- INT type converted to VARCHAR type
SELECT NVL2(null,'an apple',124) res FROM DUAL;
RES   
----- 
124  
```

```ebnf+diagram
cast::= CAST "("expr AS type_name [constraint] [DEFAULT replace_expr ON CONVERSION ERROR] ")"
```

The syntax for constraint is as follows:
```ebnf+diagram
syntax::= { "(" precision ["," scale] ")" | RANGE low_value ".." high_value}
```

The CAST function converts the value of [expr](../General SQL Syntax/expr) to the specified data type and returns the result in the new type. This function supports both basic data types and collection types.

YashanDB supports the following type conversions:

|expr type |type_name |
|-----------------------------|----------------------------------------------------------------------|
| BOOLEAN                     | TINYINT, SMALLINT, INT, BIGINT, CHAR, VARCHAR, NCHAR, NVARCHAR              |
| BIT                         | Numeric types except FLOAT, DOUBLE, character data                                      |
| CHAR, VARCHAR                | All data types except UDT                                               |
| NCHAR, NVARCHAR              | All data types except UDT                                               |
| FLOAT, DOUBLE                | Numeric types and character data except BIT                                       |
| NUMBER                      | All numeric types and character data                                     |
| TINYINT, SMALLINT, INT, BIGINT | All numeric types and character data                                     |
| TIME                        | Character data, DATE, TIMESTAMP, TIMESTAMP TZ, TIMESTAMP LTZ, INTERVAL DAY TO SECOND |
| DATE                        | Character data, TIMESTAMP, TIMESTAMP TZ, TIMESTAMP LTZ, TIME                   |
| TIMESTAMP                   | Character data, DATE, TIMESTAMP TZ, TIMESTAMP LTZ, TIME                          |
| TIMESTAMP WITH LOCAL TIME   | Character data, DATE, TIMESTAMP, TIMESTAMP TZ, TIME                              |
| TIMESTAMP WITH TIME ZONE    | Character data, DATE, TIMESTAMP, TIMESTAMP LTZ, TIME                             |
| INTERVAL YEAR TO MONTH      | Character data                                                             |
| INTERVAL DAY TO SECOND      | Character data                                                             |
| CLOB                        | Character data, JSON                                                        |
| NCLOB                       | Character data, JSON                                                        |
| BLOB                        | Character data, JSON, RAW, UROWID                                           |
| BFILE                       | Character data, JSON, CLOB, NCLOB, BLOB                                    |
| ROWID                       | Character data, RAW, UROWID                                                |
| UROWID                      | Character data, RAW                                                         |
| JSON                        | Character data, CLOB, BLOB, NCLOB, RAW                                     |
| RAW                         | Character data, CLOB, BLOB, NCLOB, UROWID                                  |

Among them:

* The content of expr must meet the format requirements of the target type.
* The value of expr cannot exceed the value range of the target type, such as 10000 cannot be converted to TINYINT type.
* If the value of expr is NULL, the function will return NULL.
* For LSC table LOB type fields, if any row of data is stored out-of-row, this function cannot be used for conversion.
* When the target type is BOOLEAN, only 'TRUE', 'T', 'YES', 'Y', '1' can be converted to TRUE, and 'FALSE', 'F', 'NO', 'N', '0' can be converted to FALSE, case insensitive.
* When converting NUMBER to BIT, the function will round down the NUMBER, and the size of the NUMBER data cannot exceed 64 bits.
* When converting TIME to other time types, the current date will be used to supplement the year, month, and day.
* When expr is a numeric constant and the target type is character type, regardless of whether cast is nested, once the length of the data converted from expr exceeds the defined length of the character type, the function will return an error: type size is too small.

**type_name**

Specifies the target type to convert to. In PL blocks, it supports conversion to SUBTYPE custom subtypes. If the subtype inherits non-scalar types or is used in SQL statements, an error will be returned.
**constraint**

**constraint**

If the target type is a character type, its size can be specified. If expr is a string type and its length exceeds the specified size, it will be truncated; for other types, exceeding the specified size will cause conversion to fail and return an Out of range error.

If the target types are CHAR or NCHAR, and the size is not specified in the statement, there is no need to consider the original data type to be converted; its estimated size length will default to 1.

If the target type is VARCHAR and the size is not specified in the statement, the estimated size will be based on the type of expr. For string types, its estimated size is the length of the original string; for other types, it is the length when that type is converted to string.

If the target type is NVARCHAR, its size must be specified in the statement, otherwise an error will be returned.

If the target type is an integer type, the RANGE specification is only supported in PL blocks and not SQL statements.

**DEFAULT replace_expr ON CONVERSION ERROR**

**DEFAULT replace_expr ON CONVERSION ERROR**

This statement can be omitted; if the conversion of expr fails, the function will return an error.

When the data type of expr is BIT, LOB type, or collection type, or when the type_name is BIT, LOB type, or collection type, this statement is not allowed.

***Example***

  

```sql

```sql
SELECT CAST('345' AS FLOAT) cast1,
SELECT CAST('345' AS FLOAT) cast1,
CAST('345.2345' AS NUMBER(6,2)) cast2,
CAST('345a' AS INT DEFAULT '' ON CONVERSION ERROR) cast3,
CAST(SYSTIMESTAMP AS CHAR(100)) cast4
FROM DUAL;
      CAST1     CAST2    CAST3 CAST4                     
----------- --------- -------- ---------------------------
  3.45E+002    345.23          2022-01-09 17:52:30.634370

SELECT CAST('abcdef' AS CHAR) cast1,
CAST(1.23456 AS VARCHAR) cast2,
CAST(NULL AS CHAR) cast3,
CAST(NULL AS VARCHAR) cast4
FROM DUAL;
CAST1 CAST2     CAST3 CAST4 
----- --------- ----- ----- 
a     1.23456

select CAST(321.1234567 AS VARCHAR(8)) FROM DUAL;
[1:28]YAS-00008 type convert error : type size is too small

select CAST(CAST(321.1234567 AS NUMBER) AS VARCHAR(8)) FROM DUAL;
[1:44]YAS-00008 type convert error : type size is too small

DECLARE
DECLARE
  SUBTYPE MY_INT IS PLS_INTEGER ;
  v1 PLS_INTEGER;
BEGIN
  v1 := CAST('9' AS MY_INT RANGE 1..10);
  DBMS_OUTPUT.PUT_LINE('value: ' || v1);
END;
/
value: 9
PL/SQL Succeed.

DECLARE
DECLARE
  SUBTYPE MY_INT IS PLS_INTEGER ;
  v1 PLS_INTEGER;
BEGIN
  SELECT CAST('9' AS MY_INT) INTO v1 FROM DUAL;
  DBMS_OUTPUT.PUT_LINE('value: ' || v1);
END;
/
YAS-04253 PL/SQL compiling errors:
[5:22] YAS-04229 invalid datatype

DECLARE
DECLARE
  TYPE MY_REC IS RECORD(a INT);
  SUBTYPE MY_INT IS MY_REC;
  v1 PLS_INTEGER;
BEGIN
  v1 := CAST('9' AS MY_INT);
  DBMS_OUTPUT.PUT_LINE('value: ' || v1);
END;
/
YAS-04253 PL/SQL compiling errors:
[6:9] YAS-04229 invalid datatype

create or replace type int_table is table of INT;

create or replace type int_table is table of INT;
/
create or replace type int_table1 is table of INT;
/
select * from table(cast(int_table(1,2,3,4) as int_table1));
COLUMN_VALUE 
------------ 
           1
           2
           3
           4

4 rows fetched.
drop type int_table;
drop type int_table1;

-- plsql
create or replace type varray_01 is varray(10) of varchar(20);
/
create or replace type varray_02 is varray(10) of varchar(10);
/
declare
  a varray_01:= varray_01(1,2,3);
  b varray_02:= varray_02(1,2,3);
  begin
  select cast(a as varray_02)into b from dual;
  DBMS_OUTPUT.PUT_LINE('b: ' || b(1) || '  ' || 'b: ' || b(3));
end;
 /
b: 1  b: 3
PL/SQL Succeed.
```

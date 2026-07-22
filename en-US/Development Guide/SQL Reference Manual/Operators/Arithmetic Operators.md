
YashanDB provides the following arithmetic operators:

|Operator |Operand |Meaning |NULL Participation |
| --- | --- | --- | --- |
| +   | Unary/Binary | Unary indicates a positive number, binary indicates addition | Result is NULL |
| \-  | Unary/Binary | Unary indicates a negative number, binary indicates subtraction | Result is NULL |
| \*  | Binary | Multiplication | Result is NULL |
| /   | Binary | Normal division | Result is NULL |
| %   | Binary | Integer division, returning the remainder | Result is NULL |

Division
----


In YashanDB, the ways to perform division are:

*   Arithmetic operators: /, %
*   Built-in functions: [MOD](../Built-in Functions/MOD), [DIV](../Built-in Functions/DIV)


**%**

Modulus operation, synonymous with the [MOD](../Built-in Functions/MOD) function.

The format is: `n2 % n1`

Meaning: The value remaining after dividing n2 by n1 is returned as the modulus result, i.e., the remainder, with the sign of the remainder consistent with n2.

If n1 is 0, instead of raising an error, it directly returns n2's value as the remainder result.

**/**

Normal division operation.



Relationship between / and [DIV](../Built-in Functions/DIV):


*   For decimals (FLOAT/DOUBLE/NUMBER), the algorithm for / is the same as for DIV.
*   For integers, / performs normal division while DIV performs integer division and returns the quotient.

Generally, 0 cannot be used as a divisor in / or DIV operations, otherwise it raises an error, but in the case of the dividend being a floating-point number, there are special treatments as follows:

*   If the dividend is NaN, the result is NaN.
*   If the dividend is a positive floating-point number or Infinity, the result is Infinity.
*   If the dividend is a negative floating-point number or -Infinity, the result is -Infinity.

Operator Precedence
-----

The operator precedence from high to low is: + (unary), - (unary) > *, /, % > +, -. Operators of the same precedence are executed from left to right.

You can use double parentheses () to adjust the desired operator precedence.

Data Types
----

The following data types may participate in arithmetic operations:

* Numeric types (excluding BIT)

* Character types

* Date and time types

### Type Conversion

When performing binary arithmetic operations, YashanDB will implicitly convert the participating data types to a unified data type, following these principles:

*   Numeric addition, subtraction, and multiplication minimum promotion rule: unifying in the order TINYINT->SMALLINT->INT->BIGINT->NUMBER->FLOAT->DOUBLE.
*   Numeric division minimum promotion rule: unifying in the order TINYINT/SMALLINT/INT/BIGINT->NUMBER->FLOAT->DOUBLE.
*   When performing operations between character and numeric data, it will be unified to numeric type.
*   When performing operations between numeric, character data and date-time data, it will be unified to date-time type.

#### (1) Integration Rules for Integer Values

Row and column header cells indicate the data types participating in the arithmetic operation; content cells indicate the unified data type; – indicates that the two data types do not support participation in the arithmetic operation.

|Data Type |Operator or Function |TINYINT |SMALLINT |INT |BIGINT* |
| --- | --- | --- | --- | --- | --- |
| **TINYINT** | +, -, \* | SMALLINT | INT | BIGINT | BIGINT/NUMBER |
|              | /            | NUMBER        | NUMBER        | NUMBER        |NUMBER|
|              | %, MOD, DIV  | SMALLINT      | INT           | BIGINT        |BIGINT/NUMBER|
| **SMALLINT** | +, -, \* | INT | INT | BIGINT | BIGINT/NUMBER |
|              | /            | NUMBER        | NUMBER        | NUMBER        |NUMBER|
|              | %, MOD, DIV  | INT           | INT           | BIGINT        |BIGINT/NUMBER|
| **INT** | +, -, \* | BIGINT | BIGINT | BIGINT | BIGINT/NUMBER |
|              | /            | NUMBER        | NUMBER        | NUMBER        |NUMBER|
|              | %, MOD, DIV  | BIGINT        | BIGINT        | BIGINT        |BIGINT/NUMBER|
| **BIGINT\*** | +, -, \* | BIGINT/NUMBER | BIGINT/NUMBER | BIGINT/NUMBER | BIGINT/NUMBER |
|              | /            | NUMBER        | NUMBER        | NUMBER        |NUMBER|
|              | %, MOD, DIV  | BIGINT/NUMBER | BIGINT/NUMBER | BIGINT/NUMBER |BIGINT/NUMBER|

> **Note**: 
>
> When the BIGINT type participates in arithmetic operations with all integer types (+, -, \*, %, MOD, DIV), or when a negative operation (-) is performed on the BIGINT type:
>
> The conversion of the result to BIGINT or NUMBER is determined internally by the system, and YashanDB defaults to unifying conversion to NUMBER. If conversion to BIGINT is required due to business needs, please contact our technical support for assistance.

#### (2) Integration Rules for Integer Values and Other Numeric Types

Row and column header cells indicate the data types participating in the arithmetic operation; content cells indicate the unified data type. (1) indicates the adoption of the previous (1) integration rules for integer values; – indicates that the two data types do not support participation in the arithmetic operation.

|Data Type |Operator or Function |Integer Value |NUMBER |FLOAT |DOUBLE |
| --- | --- | --- | --- | --- | --- |
| **Integer Value** | +, -, \* | (1) | NUMBER | FLOAT | DOUBLE |
|              | /            | (1)        | NUMBER     | FLOAT     |DOUBLE|
|              | %, MOD, DIV  | (1)        | NUMBER     | FLOAT     |DOUBLE|
| **NUMBER** | +, -, \* | NUMBER | NUMBER | FLOAT | DOUBLE |
|              | /            | NUMBER       | NUMBER     | FLOAT     |DOUBLE|
|              | %, MOD, DIV  | NUMBER       | NUMBER     | FLOAT     |DOUBLE|
| **FLOAT** | +, -, \* | FLOAT | FLOAT | FLOAT | DOUBLE |
|              | /            | FLOAT        | FLOAT      | FLOAT     |DOUBLE|
|              | %, MOD, DIV  | FLOAT        | FLOAT      | FLOAT     |DOUBLE|
| **DOUBLE** | +, -, \* | DOUBLE | DOUBLE | DOUBLE | DOUBLE |
|              | /            | DOUBLE       | DOUBLE     | DOUBLE    |DOUBLE|
|              | %, MOD, DIV  | DOUBLE       | DOUBLE     | DOUBLE    |DOUBLE|

#### (3) Integration Rules for Numeric Types and Character Types

Row and column header cells indicate the data types participating in the arithmetic operation; content cells indicate the unified data type, (2) indicates adoption of the previous (2) integration rules for integer values and other numeric types; – indicates that the two data types do not support participation in the arithmetic operation.

|Data Type |Operator or Function |Integer Value |NUMBER |FLOAT |DOUBLE |Character Type |
| --- | --- | --- | --- | --- | --- | --- |
| **Integer Value** | +, -, \*, /, %, MOD, DIV | (2) | (2) | (2) | (2) | NUMBER |
| **NUMBER** | +, -, \*, /, %, MOD, DIV | (2) | (2) | (2) | (2) | NUMBER |
| **FLOAT** | +, -, \*, /, %, MOD, DIV | (2) | (2) | (2) | (2) | FLOAT |
| **DOUBLE** | +, -, \*, /, %, MOD, DIV | (2) | (2) | (2) | (2) | DOUBLE |
| **Character Type** | +, -, \*, /, %, MOD, DIV | NUMBER | NUMBER | FLOAT | DOUBLE | NUMBER |

#### (4) Integration Rules for Numeric Types, Character Types, and Date-Time Types

Row and column header cells indicate the data types participating in the arithmetic operation; content cells indicate the unified data type, (3) indicates adopting the previous (3) integration rules for numeric types and character types; – indicates that the two data types do not support participation in the arithmetic operation; YM indicates INTERVAL YEAR TO MONTH, DS indicates INTERVAL DAY TO SECOND, TS indicates TIMESTAMP, LTZ indicates TIMESTAMP WITH LOCAL TIME ZONE, TZ indicates TIMESTAMP WITH TIME ZONE.

|Data Type |Operator |Numeric Type |Character Type |TIME |DATE |TS |LTZ |TZ |YM |DS |
| --- |----| --- | --- | --- | --- | --- |-----|----- |-----|-----|
| **Numeric Type** | +  | (3) | (3) | * Integer, NUMBER with TIME operation: returns NUMBER<br/>* Floating point types with TIME operation: returns DOUBLE  | DATE | DATE | DATE | DATE | \-- | \-- |
|                  | \- | (3)        | (3)        | * Integer, NUMBER with TIME operation: returns NUMBER<br/>* Floating point types with TIME operation: returns DOUBLE | \--   | \-- | \--          | \--           | \-- | \-- |
|                  | \* | (3)        | (3)        | \-- | \--   | \-- | \--              | \--           | YM  | DS  |
|                  | \/ | (3)        | (3)        | \-- | \--   | \-- | \--              | \--           | \-- | \-- |
| **Character Type** | + | (3) | (3) | Only CHAR, VARCHAR types support operations with TIME: returns DOUBLE | DATE | DATE | DATE | DATE | \-- | \-- |
|                  | \- | (3) | (3) | Only CHAR, VARCHAR types support operations with TIME: returns DOUBLE |  \-- |  \-- |  \-- |  \-- | \-- | \-- | 
|                  | \*<br/>/  | (3)        | (3)        | \-- | \--      | \-- | \--       | \--           | YM   |DS|
| **TIME** | + | * With Integer, NUMBER: returns NUMBER<br/>* With floating point types: returns DOUBLE | Only supports operations with CHAR, VARCHAR types: returns DOUBLE | \-- | TS | TS | \-- | TIME | -- | DS |
|                  | \-     | * With Integer, NUMBER: returns NUMBER<br/>* With floating point types: returns DOUBLE         | Only supports operations with CHAR, VARCHAR types: returns DOUBLE        | DS | \-- | \--  | \--              |TIME| -- | DS |
|                  | *<br/>/    | * With Integer, NUMBER: returns NUMBER<br/>* With floating point types: returns DOUBLE | Only supports operations with CHAR, VARCHAR types: returns DOUBLE | \--  | \--          | \--   | \--       | \--           | \--              |\--|
| **DATE** | + | DATE | DATE | TS | \-- | \-- | \-- | \-- | DATE | DATE |
|                  | \-     | DATE         | DATE         | TS | NUMBER | DS | DS | DS | DATE             |DATE|
| **TS** | + | DATE | DATE | TS | \-- | \-- | \-- | \-- | TS | TS |
|                  | \-     | DATE    | DATE    | TS   | DS | DS | DS | DS | TS      |TS|
| **LTZ** | + | DATE | DATE | LTZ | \-- | \-- | \-- | \-- | LTZ | LTZ |
|                  | \-     | DATE    | DATE    | LTZ   | DS | DS | DS | DS | LTZ      |LTZ|
| **TZ** | + | DATE | DATE | TZ | \-- | \-- | \-- | \-- | TZ | TZ |
|                  | \-     | DATE    | DATE    | TZ   | DS | DS | DS | DS | TZ      |TZ|
| **YM** | + | \-- | \-- | \-- | DATE | TS | LTZ | TZ | YM | \-- |
|                  | \-     | \--          | \--          | \-- |  \--          | \-- |  \--          | \--           | YM   |\--|
|                  | \*     | YM | -- | \--          | \--           | \-- |  \--          | \--              |\--|\--|
|                  | /      | YM | -- | \--          | \--           | \-- |  \--          | \--              |\--|\--|
| **DS** | + | \-- | \-- | TIME | DATE | TS | LTZ | TZ | \-- | DS |
|                  | \-     | \--          | \--   | \--       | \--   | \--       | \--          | \--           | \-- |DS|
|                  | \*     | DS | -- | \--  | \--          | \--   | \--       | \--           | \--              |\--|
|                  | /      | DS | -- | \--  | \--         | \--   | \--       | \--           | \--              |\--|

***Example***

```sql
-- When performing arithmetic between character type and numeric type, the character type converts to NUMBER type before the calculation.
SELECT '44'/2 char_cal,
typeof('44'/2) type_trans
FROM dual;
   CHAR_CAL TYPE_TRANS
----------- ----------
         22 number
   
-- When calculating INT type and FLOAT type, the INT type is converted to FLOAT type for calculation.
SELECT 2*CAST(2 AS FLOAT) int_cal,
typeof(2*CAST(2 AS FLOAT)) type_trans
FROM dual;
    INT_CAL TYPE_TRANS                                                     
----------- ----------------------------------------------------------------
   4.0E+000 float
```

### Date-Time Arithmetic Operations

Date-time data can participate in the following arithmetic operations: LTZ indicates TIMESTAMP WITH LOCAL TIME ZONE, TZ indicates TIMESTAMP WITH TIME ZONE.

|Operator |Operand |Return Type |Operation Rules |
| --- | --- | --- | --- |
| +   | Binary | *   Time | *   Adding two interval types returns a longer interval type. <br>*   Adding DATE, TIMESTAMP, LTZ, TZ with TIME types returns a TIMESTAMP, LTZ, TZ that is later on the timeline.<br>*   Adding TIME, DATE, TIMESTAMP, LTZ, TZ with an interval type returns a TIME, DATE, TIMESTAMP, LTZ, TZ that is later on the timeline.<br>*   Adding DATE, TIMESTAMP, LTZ, TZ with a numeric value interprets the numeric value as days (the fractional part indicates non-whole days), returning a DATE that is later on the timeline. |
| \-  | Binary | *   Time Interval<br>*   Time | *   Subtracting two interval types returns a shorter interval type.<br>*   Subtracting DATE, TIMESTAMP, LTZ, TZ with TIME returns a TIMESTAMP, LTZ, TZ that is earlier on the timeline.<br>*   Subtracting two DATEs returns a numeric value representing the difference in days.<br>*   Subtracting DATE with TIMESTAMP, or TIMESTAMP with TIMESTAMP, or LTZ with LTZ, or TZ with TZ, returns DS_INTERVAL.<br>*   Subtracting a DATE, TIMESTAMP, LTZ, or TZ with a numeric value interprets the numeric value as days (the fractional part indicates non-whole days), returning a DATE that is earlier on the timeline. |
| \*  | Binary | *   Time Interval<br>*   Time | *   Multiplying an interval type by a numeric value returns an interval that is scaled. |
| /   | Binary | *   Time Interval<br>*   Time | *   Dividing an interval type by a numeric value returns an interval that is scaled down. |

Below are the detailed situations supported by time types and time interval types for operations, where (4) indicates the adoption of the previous (4) integration rules for numeric types, character types, and date-time types; YM indicates INTERVAL YEAR TO MONTH, DS indicates INTERVAL DAY TO SECOND, TS indicates TIMESTAMP:

|Data Type |TIME |DS |YM |DATE |TS |LTZ |TZ |
| --- |  --- | --- | --- | --- | --- | --- | --- |
| TIME | Support operation: -<br>Return type: DS | Support operation: +-<br>Return type: TIME | N/A | Support operation: +<br>Return type: TS | Support operation: +<br>Return type: TS | Support operation: +<br>Return type: LTZ | Support operation: +<br>Return type: TZ |
| DS |  Support operation: + <br>Return type: TIME | Support operation: +-<br>Return type: DS | N/A | Support operation: +<br>Return type: DATE | Support operation: +<br>Return type: LTZ | Support operation: +<br>Return type: TZ |
| YM |  N/A | N/A | Support operation: +-<br>Return type: YM | Support operation: +<br>Return type: DATE | Support operation: +<br>Return type: TS | Support operation: +<br>Return type: LTZ | Support operation: +<br>Return type: TZ |
| DATE |  Support operation: +-<br>Return type: TS | Support operation: +-<br>Return type: DATE | Support operation: +-<br>Return type: DATE | Support operation: -<br>Return type: NUMBER | Support operation: -<br>Return type: DS | Support operation: -<br>Return type: DS | Support operation: -<br>Return type: DS |
| TS |  Support operation: +-<br>Return type: TS | Support operation: +-<br>Return type: TS | Support operation: +-<br>Return type: TS | Support operation: -<br>Return type: DS | Support operation: -<br>Return type: DS | Support operation: -<br>Return type: DS | Support operation: -<br>Return type: DS |
| LTZ | Support operation: +-<br>Return type: LTZ | Support operation: +-<br>Return type: LTZ | Support operation: +-<br>Return type: LTZ | Support operation: -<br>Return type: DS | Support operation: -<br>Return type: DS | Support operation: -<br>Return type: DS | Support operation: -<br>Return type: DS |
| TZ |  Support operation: +-<br>Return type: TZ | Support operation: +-<br>Return type: TZ | Support operation: +-<br>Return type: TZ | Support operation: -<br>Return type: DS | Support operation: -<br>Return type: DS | Support operation: -<br>Return type: DS | Support operation: -<br>Return type: DS |

***Example***

```sql
CREATE TABLE date_dd(a DATE, b DATE);
INSERT INTO date_dd VALUES('2008-12-31','2018-12-31');
COMMIT;
     
-- The operator '-' returns a time interval
SELECT b-a FROM date_dd;
        B-A
-----------
       3652
     
-- The operator '-' returns a time
SELECT b-1 FROM date_dd;
B-1                          
--------------------------------
2018-12-30 00:00:00                 
     
SELECT sysdate+2 FROM DUAL;
SYSDATE+2                    
--------------------------------
2021-06-10 11:07:47                  
    
-- DATE and TIMESTAMP with numeric types for addition/subtraction; the numeric value represents days
CREATE TABLE date_dt(c1 DATE, c2 TIMESTAMP);
INSERT INTO date_dt VALUES ('2020-03-31', '2020-03-31 12:30:59.999999');
COMMIT;
     
SELECT c1-2.5, c2-2.5 FROM date_dt;
C1-2.5                           C2-2.5
-------------------------------- --------------------------------
2020-03-28 12:00:00              2020-03-29 00:30:59
     
SELECT c1+'2.5', c2+'2.5' FROM date_dt;
C1+'2.5'                         C2+'2.5'
-------------------------------- --------------------------------
2020-04-02 12:00:00              2020-04-03 00:30:59    
     
SELECT c1 FROM date_dt;
C1
--------------------------------
2020-03-31 00:00:00             

-- The output result is from TIME type operation
CREATE TABLE date_t(c1 TIME);
INSERT INTO date_t VALUES ('23:59:59.999999');
COMMIT;
     
SELECT c1+INTERVAL '5' HOUR FROM date_t;
C1+INTERVAL'5'HOUR
--------------------
04:59:59.999999
```

If the output date is invalid, it will raise an error.

***Example***

```sql
-- Performing the operation on the fields in the previous example data_dt yields April 31, an invalid date
SELECT C1+INTERVAL '01-01' YEAR TO MONTH FROM date_dt;
-- heap table output
YAS-00008 type convert error : not a valid month
-- LSC table output
YAS-05012 date not valid for month specified
```

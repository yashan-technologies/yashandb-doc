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
*   Built-in functions: [MOD](../Built-in Functions/MOD), [DIV](../../../All Manuals/开发手册/SQL参考手册/内置函数/DIV)

**%**

Modulus operation, synonymous with the [MOD](../Built-in Functions/MOD) function.

The format is: `n2 % n1`

Meaning: The value remaining after dividing n2 by n1 is returned as the modulus result, i.e., the remainder, with the sign of the remainder consistent with n2.

If n1 is 0, instead of raising an error, it directly returns n2's value as the remainder result. 

**/**

Normal division operation.


Relationship between / and [DIV](../../../All Manuals/开发手册/SQL参考手册/内置函数/DIV):

*   For decimals (FLOAT/DOUBLE/NUMBER), the algorithm for / is the same as for DIV.
*   For integers, / performs normal division while DIV performs integer division and returns the quotient.

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

### Rules of Operations

When performing binary arithmetic operations, YashanDB will implicitly convert the participating data types to a unified data type, following these principles:

*   Numeric addition, subtraction, and multiplication minimum promotion rule: unifying in the order TINYINT->SMALLINT->INT->BIGINT->NUMBER->FLOAT->DOUBLE.
*   Numeric division minimum promotion rule: unifying in the order TINYINT/SMALLINT/INT/BIGINT->NUMBER->FLOAT->DOUBLE.
*   When performing operations between character and numeric data, it will be unified to numeric type.
*   When performing operations between numeric, character data and date-time data, it will be unified to date-time type.

|Data Type |Operator |TINYINT       |TINYINT UNSIGNED |SMALLINT     |SMALLINT UNSIGNED |INT     |INT UNSIGNED    |
| ---------------------- | ------------ | ---------------- | ----------------- | ---------------- | ------------------ | ---------------- | ---------------- |
| TINYINT                | +, -, \*     | SMALLINT         | INT UNSIGNED     | INT              | INT UNSIGNED      | BIGINT           | BIGINT UNSIGNED |
|                        | /, DIV       | DECIMAL          | DECIMAL           | DECIMAL          | DECIMAL            | DECIMAL          | DECIMAL          |
|                        | %,  MOD      | SMALLINT         | INT               | INT              | INT                | BIGINT           | BIGINT           |
| TINYINT UNSIGNED      | +, -, \*     | INT UNSIGNED    | INT UNSIGNED     | INT UNSIGNED    | INT UNSIGNED      | BIGINT UNSIGNED | BIGINT UNSIGNED |
|                        | /, DIV       | DECIMAL          | DECIMAL           | DECIMAL          | DECIMAL            | DECIMAL          | DECIMAL          |
|                        | %,  MOD      | INT UNSIGNED    | INT UNSIGNED     | INT UNSIGNED    | INT UNSIGNED      | BIGINT UNSIGNED | BIGINT UNSIGNED |
| SMALLINT               | +, -, \*     | INT              | INT UNSIGNED     | INT              | INT UNSIGNED      | BIGINT           | BIGINT UNSIGNED |
|                        | /, DIV       | DECIMAL          | DECIMAL           | DECIMAL          | DECIMAL            | DECIMAL          | DECIMAL          |
|                        | %,  MOD      | INT              | INT               | INT              | INT                | BIGINT           | BIGINT           |
| SMALLINT UNSIGNED     | +, -, \*     | INT UNSIGNED    | INT UNSIGNED     | INT UNSIGNED    | INT UNSIGNED      | BIGINT UNSIGNED | BIGINT UNSIGNED |
|                        | /, DIV       | DECIMAL          | DECIMAL           | DECIMAL          | DECIMAL            | DECIMAL          | DECIMAL          |
|                        | %,  MOD      | INT UNSIGNED    | INT UNSIGNED     | INT UNSIGNED    | INT UNSIGNED      | BIGINT UNSIGNED | BIGINT UNSIGNED |
| INT                    | +, -, \*     | BIGINT           | BIGINT UNSIGNED  | BIGINT           | BIGINT UNSIGNED   | BIGINT           | BIGINT UNSIGNED |
|                        | /, DIV       | DECIMAL          | DECIMAL           | DECIMAL          | DECIMAL            | DECIMAL          | DECIMAL          |
|                        | %,  MOD      | BIGINT           | BIGINT            | BIGINT           | BIGINT             | BIGINT           | BIGINT           |
| INT UNSIGNED          | +, -, \*     | BIGINT UNSIGNED | BIGINT UNSIGNED  | BIGINT UNSIGNED | BIGINT UNSIGNED   | BIGINT UNSIGNED | BIGINT UNSIGNED |
|                        | /, DIV       | DECIMAL          | DECIMAL           | DECIMAL          | DECIMAL            | DECIMAL          | DECIMAL          |
|                        | %,  MOD      | BIGINT UNSIGNED | BIGINT UNSIGNED  | BIGINT UNSIGNED | BIGINT UNSIGNED   | BIGINT UNSIGNED | BIGINT UNSIGNED |
| BIGINT                 | +, -, \*     | DECIMAL          | BIGINT UNSIGNED  | DECIMAL          | BIGINT UNSIGNED   | DECIMAL          | BIGINT UNSIGNED |
|                        | /, DIV       | DECIMAL          | DECIMAL           | DECIMAL          | DECIMAL            | DECIMAL          | DECIMAL          |
|                        | %,  MOD      | DECIMAL          | BIGINT            | DECIMAL          | BIGINT             | DECIMAL          | BIGINT           |
| BIGINT UNSIGNED       | +, -, \*     | BIGINT UNSIGNED | BIGINT UNSIGNED  | BIGINT UNSIGNED | BIGINT UNSIGNED   | BIGINT UNSIGNED | BIGINT UNSIGNED |
|                        | /, DIV       | DECIMAL          | DECIMAL           | DECIMAL          | DECIMAL            | DECIMAL          | DECIMAL          |
|                        | %,  MOD      | BIGINT UNSIGNED | BIGINT UNSIGNED  | BIGINT UNSIGNED | BIGINT UNSIGNED   | BIGINT UNSIGNED | BIGINT UNSIGNED |
| DECIMAL                | +, -, \*     | DECIMAL          | DECIMAL           | DECIMAL          | DECIMAL            | DECIMAL          | DECIMAL          |
|                        | /, DIV       | DECIMAL          | DECIMAL           | DECIMAL          | DECIMAL            | DECIMAL          | DECIMAL          |
|                        | %,  MOD      | DECIMAL          | DECIMAL           | DECIMAL          | DECIMAL            | DECIMAL          | DECIMAL          |
| FLOAT                  | +, -, \*     | FLOAT            | FLOAT             | FLOAT            | FLOAT              | FLOAT            | FLOAT            |
|                        | /, DIV       | FLOAT            | FLOAT             | FLOAT            | FLOAT              | FLOAT            | FLOAT            |
|                        | %,  MOD      | FLOAT            | FLOAT             | FLOAT            | FLOAT              | FLOAT            | FLOAT            |
| DOUBLE                 | +, -, \*     | DOUBLE           | DOUBLE            | DOUBLE           | DOUBLE             | DOUBLE           | DOUBLE           |
|                        | /, DIV       | DOUBLE           | DOUBLE            | DOUBLE           | DOUBLE             | DOUBLE           | DOUBLE           |
|                        | %,  MOD      | DOUBLE           | DOUBLE            | DOUBLE           | DOUBLE             | DOUBLE           | DOUBLE           |
| CHAR/VARCHAR           | +, -, \*     | DECIMAL          | DECIMAL           | DECIMAL          | DECIMAL            | DECIMAL          | DECIMAL          |
|                        | /, DIV       | DECIMAL          | DECIMAL           | DECIMAL          | DECIMAL            | DECIMAL          | DECIMAL          |
|                        | %,  MOD      | DECIMAL          | DECIMAL           | DECIMAL          | DECIMAL            | DECIMAL          | DECIMAL          |
| TIMESTAMP              | +, -, \*     | DECIMAL          | BIGINT UNSIGNED  | DECIMAL          | BIGINT UNSIGNED   | DECIMAL          | BIGINT UNSIGNED |
|                        | /, DIV       | DECIMAL          | DECIMAL           | DECIMAL          | DECIMAL            | DECIMAL          | DECIMAL          |
|                        | %,  MOD      | -                | DECIMAL           | -                | DECIMAL            | -                | DECIMAL          |
| TIME                   | +, -, \*     | DECIMAL          | BIGINT UNSIGNED  | DECIMAL          | BIGINT UNSIGNED   | DECIMAL          | BIGINT UNSIGNED |
|                        | /, DIV       | DECIMAL          | DECIMAL           | DECIMAL          | DECIMAL            | DECIMAL          | DECIMAL          |
|                        | %,  MOD      | DECIMAL          | BIGINT            | DECIMAL          | BIGINT             | DECIMAL          | BIGINT           |
| DATE                   | +, -, \*     | BIGINT           | INT UNSIGNED     | BIGINT           | INT UNSIGNED      | BIGINT           | BIGINT UNSIGNED |
|                        | /, DIV       | DECIMAL          | DECIMAL           | DECIMAL          | DECIMAL            | DECIMAL          | DECIMAL          |
|                        | %,  MOD      | BIGINT           | BIGINT            | BIGINT           | BIGINT             | BIGINT           | BIGINT           |
| BINARY                 | +, -, \*     | DOUBLE           | -                 | DOUBLE           | -                  | DOUBLE           | -                |
|                        | /, DIV       | DOUBLE           | -                 | DOUBLE           | -                  | DOUBLE           | -                |
|                        | %,  MOD      | DOUBLE           | -                 | DOUBLE           | -                  | DOUBLE           | -                |
| CLOB                   | +, -, \*     | -                | -                 | -                | -                  | -                | -                |
|                        | /, DIV       | -                | -                 | -                | -                  | -                | -                |
|                        | %,  MOD      | -                | -                 | -                | -                  | -                | -                |

|Data Type |Operator |BIGINT     |BIGINT UNSIGNED |DECIMAL |FLOAT  |DOUBLE |CHAR/VARCHAR    |
| ---------------------- | ------------ | ---------------- | ---------------- | ------- | ------ | ------ | ------- |
| TINYINT                | +, -, *      | DECIMAL          | BIGINT UNSIGNED | DECIMAL | FLOAT  | DOUBLE | DECIMAL |
|                        | /, DIV       | DECIMAL          | DECIMAL          | DECIMAL | FLOAT  | DOUBLE | DECIMAL |
|                        | %,  MOD      | DECIMAL          | BIGINT           | DECIMAL | FLOAT  | DOUBLE | DECIMAL |
| TINYINT UNSIGNED      | +, -, *      | BIGINT UNSIGNED | BIGINT UNSIGNED | DECIMAL | FLOAT  | DOUBLE | DECIMAL |
|                        | /, DIV       | DECIMAL          | DECIMAL          | DECIMAL | FLOAT  | DOUBLE | DECIMAL |
|                        | %,  MOD      | BIGINT UNSIGNED | BIGINT UNSIGNED | DECIMAL | FLOAT  | DOUBLE | DECIMAL |
| SMALLINT               | +, -, *      | DECIMAL          | BIGINT UNSIGNED | DECIMAL | FLOAT  | DOUBLE | DECIMAL |
|                        | /, DIV       | DECIMAL          | DECIMAL          | DECIMAL | FLOAT  | DOUBLE | DECIMAL |
|                        | %,  MOD      | DECIMAL          | BIGINT           | DECIMAL | FLOAT  | DOUBLE | DECIMAL |
| SMALLINT UNSIGNED     | +, -, *      | BIGINT UNSIGNED | BIGINT UNSIGNED | DECIMAL | FLOAT  | DOUBLE | DECIMAL |
|                        | /, DIV       | DECIMAL          | DECIMAL          | DECIMAL | FLOAT  | DOUBLE | DECIMAL |
|                        | %,  MOD      | BIGINT UNSIGNED | BIGINT UNSIGNED | DECIMAL | FLOAT  | DOUBLE | DECIMAL |
| INT                    | +, -, *      | DECIMAL          | BIGINT UNSIGNED | DECIMAL | FLOAT  | DOUBLE | DECIMAL |
|                        | /, DIV       | DECIMAL          | DECIMAL          | DECIMAL | FLOAT  | DOUBLE | DECIMAL |
|                        | %,  MOD      | DECIMAL          | BIGINT           | DECIMAL | FLOAT  | DOUBLE | DECIMAL |
| INT UNSIGNED          | +, -, *      | BIGINT UNSIGNED | BIGINT UNSIGNED | DECIMAL | FLOAT  | DOUBLE | DECIMAL |
|                        | /, DIV       | DECIMAL          | DECIMAL          | DECIMAL | FLOAT  | DOUBLE | DECIMAL |
|                        | %,  MOD      | BIGINT UNSIGNED | BIGINT UNSIGNED | DECIMAL | FLOAT  | DOUBLE | DECIMAL |
| BIGINT                 | +, -, *      | DECIMAL          | BIGINT UNSIGNED | DECIMAL | FLOAT  | DOUBLE | DECIMAL |
|                        | /, DIV       | DECIMAL          | DECIMAL          | DECIMAL | FLOAT  | DOUBLE | DECIMAL |
|                        | %,  MOD      | DECIMAL          | BIGINT           | DECIMAL | FLOAT  | DOUBLE | DECIMAL |
| BIGINT UNSIGNED       | +, -, *      | BIGINT UNSIGNED | BIGINT UNSIGNED | DECIMAL | FLOAT  | DOUBLE | DECIMAL |
|                        | /, DIV       | DECIMAL          | DECIMAL          | DECIMAL | FLOAT  | DOUBLE | DECIMAL |
|                        | %,  MOD      | BIGINT UNSIGNED | BIGINT UNSIGNED | DECIMAL | FLOAT  | DOUBLE | DECIMAL |
| DECIMAL                | +, -, *      | DECIMAL          | DECIMAL          | DECIMAL | FLOAT  | DOUBLE | DECIMAL |
|                        | /, DIV       | DECIMAL          | DECIMAL          | DECIMAL | FLOAT  | DOUBLE | DECIMAL |
|                        | %,  MOD      | DECIMAL          | DECIMAL          | DECIMAL | FLOAT  | DOUBLE | DECIMAL |
| FLOAT                  | +, -, *      | FLOAT            | FLOAT            | FLOAT   | FLOAT  | DOUBLE | FLOAT   |
|                        | /, DIV       | FLOAT            | FLOAT            | FLOAT   | FLOAT  | DOUBLE | FLOAT   |
|                        | %,  MOD      | FLOAT            | FLOAT            | FLOAT   | FLOAT  | DOUBLE | FLOAT   |
| DOUBLE                 | +, -, *      | DOUBLE           | DOUBLE           | DOUBLE  | DOUBLE | DOUBLE | DOUBLE  |
|                        | /, DIV       | DOUBLE           | DOUBLE           | DOUBLE  | DOUBLE | DOUBLE | DOUBLE  |
|                        | %,  MOD      | DOUBLE           | DOUBLE           | DOUBLE  | DOUBLE | DOUBLE | DOUBLE  |
| CHAR/VARCHAR                   | +, -, *      | DECIMAL          | DECIMAL          | DECIMAL | FLOAT  | DOUBLE | DECIMAL |
|                        | /, DIV       | DECIMAL          | DECIMAL          | DECIMAL | FLOAT  | DOUBLE | DECIMAL |
|                        | %,  MOD      | DECIMAL          | DECIMAL          | DECIMAL | FLOAT  | DOUBLE | DECIMAL |
| TIMESTAMP              | +, -, *      | DECIMAL          | BIGINT UNSIGNED | DECIMAL | DOUBLE | DOUBLE | DOUBLE  |
|                        | /, DIV       | DECIMAL          | DECIMAL          | DECIMAL | DOUBLE | DOUBLE | DOUBLE  |
|                        | %,  MOD      | -                | DECIMAL          | DECIMAL | DOUBLE | DOUBLE | DOUBLE  |
| TIME                   | +, -, *      | DECIMAL          | BIGINT UNSIGNED | DECIMAL | DOUBLE | DOUBLE | DOUBLE  |
|                        | /, DIV       | DECIMAL          | DECIMAL          | DECIMAL | DOUBLE | DOUBLE | DOUBLE  |
|                        | %,  MOD      | DECIMAL          | BIGINT           | DECIMAL | DOUBLE | DOUBLE | DOUBLE  |
| DATE                   | +, -, *      | BIGINT           | BIGINT UNSIGNED | DECIMAL | DOUBLE | DOUBLE | DOUBLE  |
|                        | /, DIV       | DECIMAL          | DECIMAL          | DECIMAL | DOUBLE | DOUBLE | DOUBLE  |
|                        | %,  MOD      | BIGINT           | BIGINT           | DECIMAL | DOUBLE | DOUBLE | DOUBLE  |
| BINARY                 | +, -, *      | DOUBLE           | -                | DOUBLE  | DOUBLE | DOUBLE | -       |
|                        | /, DIV       | DOUBLE           | -                | DOUBLE  | DOUBLE | DOUBLE | -       |
|                        | %,  MOD      | DOUBLE           | -                | DOUBLE  | DOUBLE | DOUBLE | -       |
| CLOB                   | +, -, *      | -                | -                | -       | -      | -      | -       |
|                        | /, DIV       | -                | -                | -       | -      | -      | -       |
|                        | %,  MOD      | -                | -                | -       | -      | -      | -       |

|Data Type |Operator |TIMESTAMP        |TIME      |DATE  |BINARY |CLOB   |
| ---------------------- | ------------ | ---------------- | ---------------- | ---------------- | ------ | ------ |
| TINYINT                | +, -, *      | DECIMAL          | DECIMAL          | BIGINT           | DOUBLE | -      |
|                        | /, DIV       | DECIMAL          | DECIMAL          | DECIMAL          | DOUBLE | -      |
|                        | %,  MOD      | DECIMAL          | DECIMAL          | BIGINT           | DOUBLE | -      |
| TINYINT UNSIGNED      | +, -, *      | BIGINT UNSIGNED | BIGINT UNSIGNED | INT UNSIGNED    | -      | -      |
|                        | /, DIV       | DECIMAL          | DECIMAL          | DECIMAL          | -      | -      |
|                        | %,  MOD      | BIGINT UNSIGNED | BIGINT UNSIGNED | BIGINT UNSIGNED | -      | -      |
| SMALLINT               | +, -, *      | DECIMAL          | DECIMAL          | BIGINT           | DOUBLE | -      |
|                        | /, DIV       | DECIMAL          | DECIMAL          | DECIMAL          | DOUBLE | -      |
|                        | %,  MOD      | DECIMAL          | DECIMAL          | BIGINT           | DOUBLE | -      |
| SMALLINT UNSIGNED     | +, -, *      | BIGINT UNSIGNED | BIGINT UNSIGNED | INT UNSIGNED    | -      | -      |
|                        | /, DIV       | DECIMAL          | DECIMAL          | DECIMAL          | -      | -      |
|                        | %,  MOD      | BIGINT UNSIGNED | BIGINT UNSIGNED | BIGINT UNSIGNED | -      | -      |
| INT                    | +, -, *      | DECIMAL          | DECIMAL          | BIGINT           | DOUBLE | -      |
|                        | /, DIV       | DECIMAL          | DECIMAL          | DECIMAL          | DOUBLE | -      |
|                        | %,  MOD      | DECIMAL          | DECIMAL          | BIGINT           | DOUBLE | -      |
| INT UNSIGNED          | +, -, *      | BIGINT UNSIGNED | BIGINT UNSIGNED | BIGINT UNSIGNED | -      | -      |
|                        | /, DIV       | DECIMAL          | DECIMAL          | DECIMAL          | -      | -      |
|                        | %,  MOD      | BIGINT UNSIGNED | BIGINT UNSIGNED | BIGINT UNSIGNED | -      | -      |
| BIGINT                 | +, -, *      | DECIMAL          | DECIMAL          | BIGINT           | DOUBLE | -      |
|                        | /, DIV       | DECIMAL          | DECIMAL          | DECIMAL          | DOUBLE | -      |
|                        | %,  MOD      | DECIMAL          | DECIMAL          | BIGINT           | DOUBLE | -      |
| BIGINT UNSIGNED       | +, -, *      | BIGINT UNSIGNED | BIGINT UNSIGNED | BIGINT UNSIGNED | -      | -      |
|                        | /, DIV       | DECIMAL          | DECIMAL          | DECIMAL          | -      | -      |
|                        | %,  MOD      | BIGINT UNSIGNED | BIGINT UNSIGNED | BIGINT UNSIGNED | -      | -      |
| DECIMAL                | +, -, *      | DECIMAL          | DECIMAL          | DOUBLE           | DOUBLE | -      |
|                        | /, DIV       | DECIMAL          | DECIMAL          | DECIMAL          | DOUBLE | -      |
|                        | %,  MOD      | DECIMAL          | DECIMAL          | DECIMAL          | DOUBLE | -      |
| FLOAT                  | +, -, *      | DOUBLE           | DOUBLE           | DOUBLE           | DOUBLE | -      |
|                        | /, DIV       | DOUBLE           | DOUBLE           | DOUBLE           | DOUBLE | -      |
|                        | %,  MOD      | DOUBLE           | DOUBLE           | DOUBLE           | DOUBLE | -      |
| DOUBLE                 | +, -, *      | DOUBLE           | DOUBLE           | DOUBLE           | DOUBLE | -      |
|                        | /, DIV       | DOUBLE           | DOUBLE           | DOUBLE           | DOUBLE | -      |
|                        | %,  MOD      | DOUBLE           | DOUBLE           | DOUBLE           | DOUBLE | -      |
| CHAR/VARCHAR           | +, -, *      | DOUBLE           | DOUBLE           | DOUBLE           | -      | -      |
|                        | /, DIV       | DOUBLE           | DOUBLE           | DOUBLE           | -      | -      |
|                        | %,  MOD      | DOUBLE           | DOUBLE           | DOUBLE           | -      | -      |
| TIMESTAMP              | +, -, *      | DECIMAL          | DECIMAL          | DECIMAL          | -      | -      |
|                        | /, DIV       | DECIMAL          | DECIMAL          | DECIMAL          | -      | -      |
|                        | %,  MOD      | DECIMAL          | DECIMAL          | DECIMAL          | -      | -      |
| TIME                   | +, -, *      | DECIMAL          | DECIMAL          | DECIMAL          | -      | -      |
|                        | /, DIV       | DECIMAL          | DECIMAL          | DECIMAL          | -      | -      |
|                        | %,  MOD      | DECIMAL          | DECIMAL          | DECIMAL          | -      | -      |
| DATE                   | +, -, *      | DECIMAL          | DECIMAL          | BIGINT           | -      | DOUBLE |
|                        | /, DIV       | DECIMAL          | DECIMAL          | DECIMAL          | -      | DOUBLE |
|                        | %,  MOD      | DECIMAL          | DECIMAL          | BIGINT           | -      | DOUBLE |
| BINARY                 | +, -      | DOUBLE          | -                | DATE            | DOUBLE | DOUBLE |
|  | * | - | - | - | DOUBLE | DOUBLE |
|                        | /, DIV       | -                | -                | -                | DOUBLE | DOUBLE |
|                        | %,  MOD      | DOUBLE         | -                | -                | DOUBLE | DOUBLE |
| CLOB                   | +, -, *      | -                | -                | DOUBLE           | DOUBLE | -      |
|                        | /, DIV       | -                | -                | DOUBLE           | DOUBLE | -      |
|                        | %,  MOD      | -                | -                | DOUBLE           | DOUBLE | -      |

> **Note**: 
>
> Under normal circumstances, 0 cannot be used as a divisor, but when 0 is used as a divisor with DOUBLE or FLOAT data types, the calculation will follow the native yashan mode calculation rules, that is, if the dividend is positive, the result will be Inf, and if the dividend is negative, the result will be -Inf.

***Example***

```sql
-- When performing arithmetic between character type and numeric type, the character type converts to NUMBER type before the calculation.
SELECT '44'/2 char_cal,
TYPEOF('44'/2) type_trans
FROM dual;
   char_cal type_trans
----------- ----------
         22 number
   
CREATE TABLE date_dd(a DATE, b DATE);
INSERT INTO date_dd VALUES('2008-12-31','2018-12-31');
COMMIT;
     
-- The operator '-' returns a time interval
SELECT b-a FROM date_dd;

                  b-a
---------------------
               100000
     
-- The operator '-' returns a time
SELECT b-1 FROM date_dd;

                  b-1
---------------------
             20181230

SELECT SYSDATE+2 FROM DUAL;

            sysdate+2
---------------------
       20251015141744         
    
-- DATE and TIMESTAMP with numeric types for addition/subtraction; the numeric value represents days
CREATE TABLE date_dt(c1 DATE, c2 TIMESTAMP);
INSERT INTO date_dt VALUES ('2020-03-31', '2020-03-31 12:30:59.999999');
SELECT * FROM date_dt;

c1                                               c2
------------------------------------------------ ----------------------------------------------------------------
2020-03-31                                       2020-03-31 12:31:00.000000

COMMIT;
     
SELECT c1-2.5, TYPEOF(c1-2.5), c2-2.5, TYPEOF(c2-2.5) FROM date_dt;

     c1-2.5 typeof(c1-2.5)                                                        c2-2.5 typeof(c2-2.5)                 
----------- ---------------------------------------------------------------- ----------- ----------------------------------------------------------------
 20200328.5 number                                                            2.0200E+13 number                         
     
SELECT c1+'2.5', TYPEOF(c1+'2.5'), c2+'2.5', TYPEOF(c2+'2.5') FROM date_dt;

   c1+'2.5' typeof(c1+'2.5')                                                    c2+'2.5' typeof(c2+'2.5')               
----------- ---------------------------------------------------------------- ----------- ----------------------------------------------------------------
  2.02E+007 double                                                             2.02E+013 double                         
      

-- The output result is from TIME type operation
CREATE TABLE date_t(c1 TIME);
INSERT INTO date_t VALUES ('23:59:59.999999');
SELECT * FROM date_t;

c1
--------------------
24:00:00.000000

COMMIT;
     
SELECT c1+INTERVAL '5' HOUR FROM date_t;

c1+INTERVAL '5' HOUR
--------------------
29:00:00.000000

SELECT C1+INTERVAL '01-01' YEAR TO MONTH FROM date_dt;

C1+INTERVAL '01-01' YEAR TO MONTH
------------------------------------------------
2021-04-30

-- Verify the operation when 0 is used as a dividend with DOUBLE or FLOAT data types via the MySQL client

CREATE TABLE td(a DOUBLE, b FLOAT);

desc td;
+-------+--------+------+------+---------+-------+
| Field | Type   | Null | Key  | Default | Extra |
+-------+--------+------+------+---------+-------+
| A     | double | YES  |      | NULL    |       |
| B     | float  | YES  |      | NULL    |       |
+-------+--------+------+------+---------+-------+

SELECT * FROM td;
+------+------+
| a    | b    |
+------+------+
|    0 | NULL |
|    0 |    0 |
+------+------+

SELECT 1/a, -1/a, 1/b,-1/b FROM td;
+------+------+------+------+
| 1/a  | -1/a | 1/b  | -1/b |
+------+------+------+------+
|  Inf | -Inf | NULL | NULL |
|  Inf | -Inf |  Inf | -Inf |
+------+------+------+------+

```
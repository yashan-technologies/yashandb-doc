YashanDB provides the following comparison operators:

|Operator |Operand |Meaning |NULL participation |
| --- | --- | --- | --- |
| =  | Binary  | Equals  | Result isNULL |
| != or <> | Binary  | Not equal | Result is NULL |
| >  | Binary  | Greater than  | Result is NULL |
| >= | Binary  | Greater than or equal | Result is NULL |
| <   | Binary  | Less than  | Result is NULL |
| <=  | Binary  | Less than or equal | Result is NULL |
| [NOT] IN | Binary  | Whether the left operand is [not] in the right operand set | IN:<br />* Left operand is NULL: Result is FALSE<br />* Right operand set contains NULL: If there are non-NULL values in the set that equal the left operand, the result is TRUE; otherwise, FALSE<br />NOT IN:<br />* Left operand is NULL: Result is FALSE<br />* Right operand set contains NULL: If the set contains NULL values, the result is FALSE; if the set does not contain NULL values and all data is not equal to the left operand, the result is TRUE; otherwise, FALSE |
| [NOT] LIKE | Binary  | Whether the left operand matches the right operand [not] | Result is NULL |
| [NOT] BETWEEN AND | Ternary  | Whether the first operand is [not] within the range defined by the second and third operands | Result is NULL |
| IS [NOT] NULL | Unary  | Whether it is [not] equal to NULL | IS NULL:<br />* Operand is NULL: Result is TRUE<br />* Operand is not NULL: Result is FALSE<br />IS NOT NULL:<br />* Operand is NULL: Result is FALSE<br />* Operand is not NULL: Result is TRUE |

For detailed usage of comparison operators in SQL syntax, please refer to the [condition condition clause](../General SQL Syntax/condition) description.

Operator Precedence
-----

From highest to lowest precedence:

*   =, != or <>, >, >=, <, <= 
*   \[NOT\] IN, IS \[NOT\] NULL, \[NOT\] LIKE 
*   \[NOT\] BETWEEN AND

Double parentheses () can be used to adjust the desired operator precedence.

Data Types
----

When performing comparison operations with =, != or <>, >, >=, <, <=, the following data types may participate in the comparison:

*   Numeric
*   Character
*   DateTime
*   Boolean
*   BINARY
*   TEXT/BLOB

### Rules of Operations

If the data types on either side of the comparison operator do not match, YashanDB will perform implicit data conversion to unify one side's data type with the other side, following these principles:

* Numeric promotion rules: unify in the order of TINYINT->SMALLINT->INT->BIGINT->NUMBER->FLOAT->DOUBLE.
* Character data will unify with other types during comparison.
* DATE type data will unify with TIMESTAMP type data during operations.
* When TIMESTAMP and DATE type data are compared for equality (=) with integers, the integer will be converted to TIMESTAMP or DATE type. If the conversion succeeds, the comparison will be performed using two TIMESTAMP or DATE values. If the conversion fails, FALSE will be returned directly.
* When TIMESTAMP and DATE type data are compared with integers using other comparison operators besides equality (=), the integer will be converted to TIMESTAMP or DATE type. If the conversion succeeds, the comparison will be performed using two TIMESTAMP or DATE values. If the conversion fails, the TIMESTAMP and DATE type data will be converted to integers for comparison.
* Boolean data will unify with numeric data during operations.

The row and column headers indicate the data types involved in the =, != or <>, >, >=, <, <= comparison operations; the content cells indicate the unified data type; - indicates that the two data types are not supported for comparison.

|Data Type |TINYINT      |TINYINT UNSIGNED  |SMALLINT      |SMALLINT UNSIGNED |INT  |INT UNSIGNED    |BIGINT      |BIGINT UNSIGNED |
| ---------------------- | ------------------- | ------------------- | ------------------- | ------------------- | ----------------- | ----------------- | ----------------- | ----------------- |
| TINYINT                | TINYINT             | TINYINT UNSIGNED  | SMALLINT            | SMALLINT UNSIGNED | INT               | INT UNSIGNED    | BIGINT            | BIGINT UNSIGNED |
| TINYINT UNSIGNED     | TINYINT UNSIGNED  | TINYINT UNSIGNED  | SMALLINT            | SMALLINT UNSIGNED | INT               | INT UNSIGNED    | BIGINT            | BIGINT UNSIGNED |
| SMALLINT               | SMALLINT            | SMALLINT            | SMALLINT            | SMALLINT UNSIGNED | INT               | INT UNSIGNED    | BIGINT            | BIGINT UNSIGNED |
| SMALLINT UNSIGNED    | SMALLINT UNSIGNED | SMALLINT UNSIGNED | SMALLINT UNSIGNED | SMALLINT UNSIGNED | INT               | INT UNSIGNED    | BIGINT            | BIGINT UNSIGNED |
| INT                    | INT                 | INT                 | INT                 | INT                 | INT               | INT UNSIGNED    | BIGINT            | BIGINT UNSIGNED |
| INT UNSIGNED         | INT UNSIGNED      | INT UNSIGNED      | INT UNSIGNED      | INT UNSIGNED      | INT UNSIGNED    | INT UNSIGNED    | BIGINT            | BIGINT UNSIGNED |
| BIGINT                 | BIGINT              | BIGINT              | BIGINT              | BIGINT              | BIGINT            | BIGINT            | BIGINT            | BIGINT UNSIGNED |
| BIGINT UNSIGNED      | BIGINT UNSIGNED   | BIGINT UNSIGNED   | BIGINT UNSIGNED   | BIGINT UNSIGNED   | BIGINT UNSIGNED | BIGINT UNSIGNED | BIGINT UNSIGNED | BIGINT UNSIGNED |
| DECIMAL                | DECIMAL             | DECIMAL             | DECIMAL             | DECIMAL             | DECIMAL           | DECIMAL           | DECIMAL           | DECIMAL           |
| FLOAT                  | FLOAT               | FLOAT               | FLOAT               | FLOAT               | FLOAT             | FLOAT             | FLOAT             | FLOAT             |
| DOUBLE                 | DOUBLE              | DOUBLE              | DOUBLE              | DOUBLE              | DOUBLE            | DOUBLE            | DOUBLE            | DOUBLE            |
| BOOLEAN                | TINYINT             | TINYINT UNSIGNED  | SMALLINT            | SMALLINT UNSIGNED | INT               | INT UNSIGNED    | BIGINT            | BIGINT UNSIGNED |
| CHAR/VARCHAR      | DECIMAL             | DECIMAL             | DECIMAL             | DECIMAL             | DECIMAL           | DECIMAL           | DECIMAL           | DECIMAL           |
| TIMESTAMP              | BIGINT              | BIGINT UNSIGNED   | BIGINT              | BIGINT UNSIGNED   | BIGINT            | BIGINT UNSIGNED | BIGINT            | BIGINT UNSIGNED |
| TIME                   | INT                 | INT UNSIGNED      | INT                 | INT UNSIGNED      | INT               | INT UNSIGNED    | BIGINT            | BIGINT UNSIGNED |
| DATE                   | INT                 | INT                 | INT                 | INT                 | INT               | INT UNSIGNED    | BIGINT            | BIGINT UNSIGNED |
| BINARY/VARBINARY      | DECIMAL             | DECIMAL             | DECIMAL             | DECIMAL             | DECIMAL           | DECIMAL           | DECIMAL           | DECIMAL           |
| TINYTEXT<br/>MEDIUMTEXT<br/>TEXT<br/>LONGTEXT      | DECIMAL             | DECIMAL             | DECIMAL             | DECIMAL             | DECIMAL           | DECIMAL           | DECIMAL           | DECIMAL           |
| TINYBLOB<br/>MEDIUMBLOB<br/>BLOB<br/>LONGBLOB      | DECIMAL             | DECIMAL             | DECIMAL             | DECIMAL             | DECIMAL           | DECIMAL           | DECIMAL           | DECIMAL           |


|Data Type |DECIMAL |FLOAT  |DOUBLE |BOOLEAN   |CHAR/VARCHAR    |TIMESTAMP       |TIME       |DATE     |
| ---------------------- | ------- | ------ | ------ | ------------------- | ------- | ----------------- | -------------- | ----------------- |
| TINYINT                | DECIMAL | FLOAT  | DOUBLE | TINYINT             | DECIMAL | BIGINT            | INT            | INT               |
| TINYINT UNSIGNED     | DECIMAL | FLOAT  | DOUBLE | TINYINT UNSIGNED  | DECIMAL | BIGINT UNSIGNED | INT UNSIGNED | INT               |
| SMALLINT               | DECIMAL | FLOAT  | DOUBLE | SMALLINT            | DECIMAL | BIGINT            | INT            | INT               |
| SMALLINT UNSIGNED    | DECIMAL | FLOAT  | DOUBLE | SMALLINT UNSIGNED | DECIMAL | BIGINT UNSIGNED | INT UNSIGNED | INT               |
| INT                    | DECIMAL | FLOAT  | DOUBLE | INT                 | DECIMAL | BIGINT            | INT            | INT               |
| INT UNSIGNED         | DECIMAL | FLOAT  | DOUBLE | INT UNSIGNED      | DECIMAL | BIGINT UNSIGNED | INT UNSIGNED | INT UNSIGNED    |
| BIGINT                 | DECIMAL | FLOAT  | DOUBLE | BIGINT              | DECIMAL | BIGINT            | -              | BIGINT            |
| BIGINT UNSIGNED      | DECIMAL | FLOAT  | DOUBLE | BIGINT UNSIGNED   | DECIMAL | BIGINT UNSIGNED | INT UNSIGNED | BIGINT UNSIGNED |
| DECIMAL                | DECIMAL | FLOAT  | DOUBLE | -                   | DECIMAL | DOUBLE            | -              | DECIMAL           |
| FLOAT                  | FLOAT   | FLOAT  | DOUBLE | -                   | FLOAT   | DOUBLE            | -              | FLOAT             |
| DOUBLE                 | DOUBLE  | DOUBLE | DOUBLE | -                   | DOUBLE  | DOUBLE            | -              | DOUBLE            |
| BOOLEAN                | -       | -      | -      | BOOLEAN             | BOOLEAN | -                 | -              | -                 |
| CHAR/VARCHAR           | DECIMAL | FLOAT  | DOUBLE | BOOLEAN             | CHAR    | BIGINT            | BIGINT         | INT               |
| TIMESTAMP              | DOUBLE  | DOUBLE | DOUBLE | -                   | BIGINT  | TIMESTAMP         | -              | DOUBLE            |
| TIME                   | -       | -      | -      | -                   | BIGINT  | -                 | TIME           | DATE              |
| DATE                   | DECIMAL | FLOAT  | DOUBLE | -                   | INT     | DOUBLE            | DATE           | INT               |
| BINARY/VARBINARY           | DECIMAL | FLOAT  | DOUBLE | BOOLEAN             | VARCHAR    | TIMESTAMP            | TIME         | DATE               |
| TINYTEXT<br/>MEDIUMTEXT<br/>TEXT<br/>LONGTEXT           | DECIMAL | FLOAT  | DOUBLE | BOOLEAN             | LONGTEXT    | BIGINT            | BIGINT         | INT               |
| TINYBLOB<br/>MEDIUMBLOB<br/>BLOB<br/>LONGBLOB           | DECIMAL | FLOAT  | DOUBLE | BOOLEAN             | LONGBLOB    | BIGINT            | BIGINT         | INT               |

| Data Type | BINARY/VARBINARY| TINYTEXT<br/>MEDIUMTEXT<br/>TEXT<br/>LONGTEXT | TINYBLOB<br/>MEDIUMBLOB<br/>BLOB<br/>LONGBLOB|
| ---------------------- | ------- | ------ | ------ |
| TINYINT              | DECIMAL  | DECIMAL | DECIMAL            | 
| TINYINT UNSIGNED     | DECIMAL  | DECIMAL | DECIMAL            |
| SMALLINT             | DECIMAL  | DECIMAL | DECIMAL            |
| SMALLINT UNSIGNED    | DECIMAL  | DECIMAL | DECIMAL            |
| INT                  | DECIMAL  | DECIMAL | DECIMAL            |
| INT UNSIGNED         | DECIMAL  | DECIMAL | DECIMAL            |
| BIGINT               | DECIMAL  | DECIMAL | DECIMAL            |
| BIGINT UNSIGNED      | DECIMAL  | DECIMAL | DECIMAL            |
| DECIMAL              | DECIMAL  | DECIMAL | DECIMAL            |
| FLOAT                | FLOAT    | FLOAT   | FLOAT            |
| DOUBLE               | DOUBLE   | DOUBLE | DOUBLE            | 
| BOOLEAN              | BOOLEAN       | BOOLEAN      | BOOLEAN      | 
| CHAR/VARCHAR         | VARCHAR | LONGBLOB  | LONGBLOB |
| TIMESTAMP              | TIMESTAMP  | TIMESTAMP | TIMESTAMP | 
| TIME                   |TIME       | TIME      | TIME     | 
| DATE                   | DATE | DATE  | DATE |
| BINARY/VARBINARY           | VARCHAR | LONGBLOB  | LONGBLOB | 
| TINYTEXT<br/>MEDIUMTEXT<br/>TEXT<br/>LONGTEXT           | LONGBLOB | LONGBLOB  | LONGBLOB |
| TINYBLOB<br/>MEDIUMBLOB<br/>BLOB<br/>LONGBLOB           | LONGBLOB | LONGBLOB  | LONGBLOB |


> **Note**: 
>
> Comparison between character types performs a case-sensitive string comparison. Please refer to the [character type](../../../Development Guide/SQL Reference Manual/Data Types/Character Types) document for detailed description.



***Example***

```sql
-- NULL participation in comparison
SELECT SYSDATE FROM DUAL WHERE NULL IN (1,NULL);
SYSDATE                        
--------------------------------

SELECT SYSDATE FROM DUAL WHERE 1 IN (1,NULL);
SYSDATE                        
--------------------------------
2025-10-15 17:05:30.000000
   
SELECT SYSDATE FROM DUAL WHERE 1 IN (2,NULL);
SYSDATE                        
--------------------------------

-- Implicit conversion when comparing character and numeric types
SELECT 1 FROM dual
WHERE '0'<4;
           1
------------
           1
   
-- Implicit conversion when comparing boolean and character types
SELECT 1 FROM dual
WHERE true like 'true%';
           1
------------
           
```


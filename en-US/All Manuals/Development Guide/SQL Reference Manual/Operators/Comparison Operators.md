YashanDB provides the following comparison operators:

|Operator |Operand |Meaning |NULL participation |
| --- | --- | --- | --- |
| =  | Binary  | Equals  | Result is FALSE |
| != or <> | Binary  | Not equal | Result is FALSE |
| >  | Binary  | Greater than  | Result is FALSE |
| >= | Binary  | Greater than or equal | Result is FALSE |
| <   | Binary  | Less than  | Result is FALSE |
| <=  | Binary  | Less than or equal | Result is FALSE |
| [NOT] IN | Binary  | Whether the left operand is [not] in the right operand set | IN:<br />* Left operand is NULL: Result is FALSE<br />* Right operand set contains NULL: If there are non-NULL values in the set that equal the left operand, the result is TRUE; otherwise, FALSE<br />NOT IN:<br />* Left operand is NULL: Result is FALSE<br />* Right operand set contains NULL: If the set contains NULL values, the result is FALSE; if the set does not contain NULL values and all data is not equal to the left operand, the result is TRUE; otherwise, FALSE |
| [NOT] LIKE | Binary  | Whether the left operand matches the right operand [not] | Result is FALSE |
| [NOT] BETWEEN AND | Ternary  | Whether the first operand is [not] within the range defined by the second and third operands | Result is FALSE |
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
*   RAW

### Type Conversion

If the data types on either side of the comparison operator do not match, YashanDB will perform implicit data conversion to unify one side's data type with the other side, following these principles:

* Numeric promotion rules: unify in the order of TINYINT->SMALLINT->INT->BIGINT->NUMBER->FLOAT->DOUBLE.
* BIT data will unify with BIGINT/NUMBER when performing operations.
* Character data will unify with other types during comparison.
* DATE type data will unify with TIMESTAMP type data during operations.
* TIMESTAMP type data will unify with TIMESTAMP WITH LOCAL TIME ZONE type data during operations.
* TIMESTAMP WITH LOCAL TIME ZONE type data will unify with TIMESTAMP WITH TIME ZONE type data during operations.
* TIME type data will unify with DS_INTERVAL type data during operations.
* Boolean data will unify with numeric data during operations.

#### (1) Numeric and Boolean Unification Rules

The row and column headers indicate the data types involved in the =, != or <>, >, >=, <, <= comparison operations; the content cells indicate the unified data type; - indicates that the two data types are not supported for comparison.

|Data Type |TINYINT |SMALLINT |INT |BIGINT |BIT |NUMBER |FLOAT |DOUBLE |BOOLEAN |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **TINYINT** | TINYINT  | SMALLINT | INT | BIGINT | BIGINT | NUMBER | FLOAT | DOUBLE | TINYINT |
| **SMALLINT** | SMALLINT | SMALLINT | INT | BIGINT | BIGINT | NUMBER | FLOAT | DOUBLE | SMALLINT |
| **INT** | INT | INT | INT | BIGINT | BIGINT | NUMBER | FLOAT | DOUBLE | INT |
| **BIGINT** | BIGINT | BIGINT | BIGINT | BIGINT | BIGINT | NUMBER | FLOAT | DOUBLE | BIGINT |
| **BIT** | BIGINT | BIGINT | BIGINT | BIGINT | BIT | NUMBER | \-- | \-- | BIGINT |
| **NUMBER** | NUMBER | NUMBER | NUMBER | NUMBER | NUMBER | NUMBER | FLOAT | DOUBLE | \-- |
| **FLOAT** | FLOAT | FLOAT | FLOAT | FLOAT | \-- | FLOAT | FLOAT | DOUBLE | \-- |
| **DOUBLE** | DOUBLE   | DOUBLE | DOUBLE | DOUBLE | \-- | DOUBLE | DOUBLE | DOUBLE | \-- |
| **BOOLEAN** | TINYINT | SMALLINT | INT | BIGINT | BIGINT | \-- | \-- | \-- | BOOLEAN |

#### (2) Numeric, Boolean, and Character Unification Rules

The row and column headers indicate the data types involved in the =, != or <>, >, >=, <, <= comparison operations; the content cells indicate the unified data type, where (1) indicates the use of the above (1) numeric and boolean unification rules; - indicates that the two data types are not supported for comparison.

|Data Type |Integer Value |BIT |NUMBER |FLOAT |DOUBLE |BOOLEAN |Character |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **Integer Value** | (1) | (1) | (1) | (1) | (1) | (1) | NUMBER |
| **BIT** | (1) | (1) | (1) | (1) | (1) | (1) | BIT |
| **NUMBER** | (1) | (1) | (1) | (1) | (1) | (1) | NUMEBR |
| **FLOAT** | (1) | (1) | (1) | (1) | (1) | (1) | FLOAT |
| **DOUBLE** | (1) | (1) | (1) | (1) | (1) | (1) | DOUBLE |
| **BOOLEAN** | (1) | (1) | (1) | (1) | (1) | (1) | BOOLEAN |
| **Character** | NUMBER | BIT | NUMBER | FLOAT | DOUBLE | BOOLEAN | Character* |

> **Note**: 
>
> Comparison between character types performs a case-sensitive string comparison. Please refer to the [character type](../Data Types (yashan Mode)/Character Types) document for detailed description.

#### (3) Numeric, Boolean, Character, and DateTime Unification Rules

The row and column headers indicate the data types involved in the =, != or <>, >, >=, <, <= comparison operations; the content cells indicate the unified data type, where (2) indicates the use of the above (2) numeric, boolean, and character unification rules; - indicates that the two data types are not supported for comparison.

|Data<br>Type |Numeric |Character |BOOLEAN |DATE |TIMESTAMP |TIMESTAMP WITH LOCAL TIME ZONE |TIMESTAMP WITH TIME ZONE |YM_<br/>INTERVAL |DS_<br/>INTERVAL |TIME |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **Numeric** | (2) | (2) | (2) | -- | -- | -- | -- | -- | -- | -- |
| **Character** | (2) | (2) | (2) | DATE | TIMESTAMP | TIMESTAMP WITH LOCAL TIME ZONE | TIMESTAMP WITH TIME ZONE | YM_<br/>INTERVAL | DS_<br/>INTERVAL | TIME |
| **BOOLEAN** | (2) | (2) | (2) | \-- | \-- | \-- | \-- | \-- | \-- | \-- |
| **DATE** | \-- | DATE | \-- | DATE | TIMESTAMP | TIMESTAMP WITH LOCAL TIME ZONE | TIMESTAMP WITH TIME ZONE | \-- | \-- | \-- |
| **TIMESTAMP** | \-- | TIMESTAMP | \-- | TIMESTAMP | TIMESTAMP | TIMESTAMP WITH LOCAL TIME ZONE | TIMESTAMP WITH TIME ZONE | \-- | \-- | \-- |
| **TIMESTAMP WITH LOCAL TIME ZONE** | \-- | TIMESTAMP WITH LOCAL TIME ZONE | \-- | TIMESTAMP WITH LOCAL TIME ZONE | TIMESTAMP WITH LOCAL TIME ZONE | TIMESTAMP WITH LOCAL TIME ZONE | TIMESTAMP WITH TIME ZONE | \-- | \-- | \-- |
| **TIMESTAMP WITH TIME ZONE** | \-- | TIMESTAMP WITH TIME ZONE | \-- | TIMESTAMP WITH TIME ZONE | TIMESTAMP WITH TIME ZONE | TIMESTAMP WITH TIME ZONE | TIMESTAMP WITH TIME ZONE | \-- | \-- | \-- |
| **YM_<br/>INTERVAL** | \-- | YM_<br/>INTERVAL | \-- | \-- | \-- | \-- | \-- | YM_<br/>INTERVAL | \-- | \-- |
| **DS_<br/>INTERVAL** | \-- | DS_<br/>INTERVAL | \-- | \-- | \-- | \-- | \-- | \-- | DS_<br/>INTERVAL | DS_<br/>INTERVAL |
| **TIME** | \-- | TIME | \-- | \-- | \-- | \-- | \-- | \-- | DS_<br/>INTERVAL | TIME |

***Example***

```sql
-- NULL participation in comparison
SELECT SYSDATE FROM DUAL WHERE NULL IN (1,NULL);
SYSDATE                        
--------------------------------

SELECT SYSDATE FROM DUAL WHERE 1 IN (1,NULL);
SYSDATE                        
--------------------------------
2021-06-08 10:05:53           
   
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
WHERE true LIKE 'true%';
           1
------------
           1
```

#### (4) RAW Type and Character Type Unification Rules

The RAW type can only be compared with itself or character types, with the following rules:

(The row and column headers indicate the data types involved in the =, != or <>, >, >=, <, <= comparison operations; the content cells indicate the unified data type.)

|Data Type |RAW |Character |
| ---------- | ---- | ------ |
| **RAW**    | RAW  | RAW    |
| **Character** | RAW  | Character |

#### (5) UDT Type Comparison Unification Rules

* The OBJECT type only supports equality and inequality operations by default. OBJECT types with defined methods support equality, inequality, greater than, greater than or equal to, less than, and less than or equal to operations, and comparisons are based on the results of those methods.
* The VARRAY type does not support equality, inequality, greater than, greater than or equal to, less than, and less than or equal to operations.
* The TABLE type supports only equality and inequality operations.
* When UDT types are nested, comparisons for TABLE types and OBJECT types without defined methods are not supported; however, other UDT types must adhere to comparison rules at each nested layer.
* Types that support equality and inequality operations also support [NOT] IN operations.
* All UDT types do not support [NOT] LIKE operations.
* Only OBJECT types with defined methods support [NOT] BETWEEN AND.
* All UDT types support IS [NOT] NULL operations.
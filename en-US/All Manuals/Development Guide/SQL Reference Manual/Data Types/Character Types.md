In YashanDB, character types include English letters, Chinese characters, numeric characters, and special characters, commonly used for storing text data. In addition, this type can flexibly participate in numerical and date-time operations through implicit data conversion.

Character types can be specifically classified into four data types: CHAR, VARCHAR, NCHAR, and NVARCHAR.

Storage Properties
----

|Type |Byte Length |
| --- |---------|
| CHAR | 1~8000  |
| VARCHAR | 1~65534 |
| NCHAR | 1~8000  |
| NVARCHAR | 1~65534 |

> **Note**: 
>
> In HEAP tables, VARCHAR/NVARCHAR columns that exceed 8000 bytes in length will be converted to LOB types for storage. If a column's data type size specified during table creation exceeds 8000 bytes, the column will be converted to LOB type for storage.
>   
> For example, in the UTF8 character set (a single character can take up to 4 bytes), defining a VARCHAR(2001 CHAR) column may store 8004 bytes of data, at which point the column will be converted to LOB type for storage.

## CHAR and VARCHAR

The CHAR data type is used to specify fixed-length strings, while the VARCHAR data type is used to specify variable-length strings.

CHARACTER can be used as an alias for CHAR, meaning the same as CHAR. VARCHAR2 and CHARACTER VARYING can be used as aliases for VARCHAR, meaning the same as VARCHAR.

Definition Format:

|Type |Syntax Format |Rules |
| --- |---------------------|--------------------------------------------------------------------------------------------------------|
| CHAR     | CHAR(Size[byte])            | Fixed-length string, Size range is [1,8000]. If Size is not specified, it defaults to 1; When the inserted string is less than Size, padding with spaces occurs; When the inserted string exceeds Size, insertion fails and an error is reported.  |
| VARCHAR  | VARCHAR(Size[byte])         | Variable-length string, Size range is [1,65534]. When the inserted string is less than Size, no padding occurs; When the inserted string exceeds Size, insertion fails and an error is reported. |
| CHAR     | CHAR(Size(char))           | Fixed-length string, Size range is [1,8000]. If Size is not specified, it defaults to 1; When the inserted string is less than Size, padding with spaces occurs, with the number of spaces equal to the missing character count; When the inserted string exceeds Size, insertion fails and an error is reported. |
| VARCHAR  | VARCHAR(Size(char))         | Variable-length string, Size range is [1,65534]. When the inserted string is less than Size, no padding occurs; When the inserted string exceeds Size, insertion fails and an error is reported. |

Size: Width, represents maximum length, categorized into byte length and character length based on optional length units. HEAP tables support both length units, while LSC tables only support byte length units.

The optional units after Size can be byte and char, respectively referring to byte-based and character-based sizes, with optional spaces allowed between byte/char and Size. If no unit is specified, it defaults to byte. Note that the length units differ from NCHAR and NVARCHAR types.

***Example*** for Heap tables

```sql
-- Define CHAR type data with different units
CREATE TABLE chartable (c1 CHAR(30),c2 CHAR(30 byte),c3 CHAR(30 CHAR));
INSERT INTO chartable VALUES('a','abc','abcccc');
COMMIT;
SELECT c1,c2,c3 FROM chartable;

C1                                C2                                C3                                                               
--------------------------------- --------------------------------- ---------------------------------------------------------------- 
a                                 abc                               abcccc                    

-- Define VARCHAR type data with different units
CREATE TABLE varchartable (v1 VARCHAR(30),v2 VARCHAR(30 byte),v3 VARCHAR(30 CHAR));
INSERT INTO varchartable VALUES('a','abc','abcccc');
COMMIT;
SELECT v1,v2,v3 FROM varchartable;

V1                                V2                                V3                                                               
--------------------------------- --------------------------------- ---------------------------------------------------------------- 
a                                 abc                               abcccc              

-- Define character type data, an error is returned if the length exceeds the range
CREATE TABLE overlimit (c1 CHAR(9000 byte),c2 CHAR(9000 CHAR));

[1:32]YAS-04204 number of column size must be between 1 and 8000
```

## NCHAR and NVARCHAR

NCHAR is used to designate fixed-length strings that support UNICODE, while NVARCHAR is used to designate variable-length strings that support UNICODE. These two character types are only applicable within databases configured with UNICODE character sets, allowing for the storage of multilingual data.

NVARCHAR2 can be used as an alias for NVARCHAR, meaning the same as NVARCHAR.

NCHAR and NVARCHAR types are only suitable for HEAP tables.

Definition Format:

|Type |Syntax Format |Rules |
| -------- | -------------- |------------------------------------------------------------------------------------------|
| NCHAR    | NCHAR(Size)             | Fixed-length string, Size range is [1,4000]. If Size is not specified, it defaults to 1; When the inserted string is less than Size, padding with spaces occurs; When the inserted string exceeds Size, insertion fails and an error is reported. |
| NVARCHAR | NVARCHAR(Size)          | Variable-length string, Size range is [1,32767]. When the inserted string is less than Size, no padding occurs; When the inserted string exceeds Size, insertion fails and an error is reported. |

Size represents the number of characters and must be an integer value; the storage length of the data is twice the Size, measured in character length.

The storage length limit for NCHAR is 8000 bytes, and for NVARCHAR it is 65534 bytes. Data exceeding these size limits will not be stored.

***Example*** for Heap tables

```sql
-- Define NCHAR type data
CREATE TABLE nchartable (c1 NCHAR(30));
INSERT INTO nchartable VALUES('a');
COMMIT;
SELECT c1 FROM nchartable;

C1                                                               
---------------------------------------------------------------- 
a                                                               

-- Define NVARCHAR type data
CREATE TABLE nvarchartable (v1 NVARCHAR(30));
INSERT INTO nvarchartable VALUES('a');
COMMIT;
SELECT v1 FROM nvarchartable;

V1                                                               
---------------------------------------------------------------- 
a             

-- Define character type data, an error is returned if the length exceeds the range
CREATE TABLE overlimit_nchar (c1 NCHAR(9000));

[1:40]YAS-04204 number of column size must be between 1 and 4000

CREATE TABLE overlimit_nvarchar (c1 NVARCHAR(40000));

[1:46]YAS-04204 number of column size must be between 1 and 32767
```

Character Type Sorting
-----

When performing sorting operations on character data:

* By default, sorting is based on the ASCII values of characters, for example, 'a' is less than 'b', '1' is less than '2'.

* Using the [NLSSORT](../Built-in Functions/NLSSORT) function, UTF8 encoded data can be sorted based on pinyin.

Character Type Comparison
-----

When comparing two character data:

* The comparison is case-sensitive.
* If both sides of the comparison operator are of CHAR or NCHAR type, YashanDB fills the shorter CHAR or NCHAR with spaces up to the length of the longer CHAR before comparing values.
* If either side of the comparison operator is of VARCHAR or NVARCHAR type, no padding occurs, and comparison is direct.

***Example***

```sql
-- Example 1: When both sides of the comparison operator are CHAR types, YashanDB pads the length before comparison
SELECT 1 FROM DUAL
WHERE CAST('A' AS CHAR(50)) = CAST('A' AS CHAR(30));
           1
------------
           1
   
-- Example 2: When either side of the comparison operator is VARCHAR type, YashanDB does not pad length, comparing directly
SELECT 1 FROM DUAL
WHERE CAST('A' AS CHAR(50)) > CAST('A' AS VARCHAR(50));
           1
------------
           1
   
SELECT 1 FROM DUAL
WHERE CAST('A' AS VARCHAR(30)) < CAST('A' AS CHAR(50));
           1
------------
           1
               
-- Example 3: When CHAR Size is not specified, it defaults to 1
SELECT CAST('A' AS CHAR) result FROM DUAL;
RESULT
------ 
A

SELECT CAST('ABC' AS CHAR) result FROM DUAL;
RESULT
------ 
A

SELECT CAST('ABC' AS CHAR(1)) result FROM DUAL;
RESULT
------ 
A

SELECT CAST('ABC' AS CHAR(2)) result FROM DUAL;
RESULT
------ 
AB
```

```ebnf+diagram
convert::= CONVERT "("expr ("," type_name [charset_info]|USING transcoding_name) ")"
charset_info::=  (CHARACTER SET charset_name|ASCII)
```

The CONVERT function converts the value of [expr](../General SQL Syntax/expr) to the specified data type or specified character set, and returns the result as a string in the new type or after character set conversion.

YashanDB supports the following type conversions:

|Type of expr |Target type type_name |
| --- | --- |
| BOOLEAN | TINYINT, SMALLINT, INT, BIGINT, CHAR, VARCHAR, NCHAR, NVARCHAR |
| BIT | Numeric and character data types, except FLOAT, DOUBLE |
| CHAR, VARCHAR | All data types except UDT |
| NCHAR, NVARCHAR | All data types except UDT |
| DATE | CHAR, VARCHAR, TIMESTAMP |
| FLOAT, DOUBLE | Numeric and character data types, except BIT |
| NUMBER | All numeric and character types |
| TIMESTAMP | Character data, DATE |
| TINYINT, SMALLINT, INT, BIGINT | All numeric and character types |
| INTERVAL YEAR TO MONTH | Character data |
| INTERVAL DAY TO SECOND | Character data |
| CLOB | Character data, JSON |
| NCLOB | Character data, JSON |
| BLOB | Character data, JSON, RAW, UROWID |
| ROWID | Character data, RAW, UROWID |
| UROWID | Character data, RAW |
| JSON | Character data, CLOB, BLOB, NCLOB, RAW |
| RAW | Character data, CLOB, BLOB, NCLOB, UROWID |

In addition to the above type conversion rules, this function also follows these rules:

* The content of expr must meet the format requirements of the target type.
* The value of expr cannot exceed the range of the target type, e.g., 10000 cannot be converted to TINYINT.
* If the value of expr is NULL, the function returns NULL.
* When the target type is BOOLEAN, its behavior is the same as querying the result after inserting data into a BOOLEAN column.
* When NUMBER converts to BIT, the function will truncate NUMBER, and the size of NUMBER data cannot exceed 64 bits.
* When TIME converts to DATE and TIMESTAMP, the current date is used to supplement the year, month, and day.
* The resulting string itself does not carry character set attributes, so when this string is used again as an input to the database, it will be parsed using the database's default character set, which may lead to unexpected errors.

**type_name**

* When the target type is a character type, its size can be specified. If expr is of string type and its length exceeds the specified size, it will be truncated. If expr is of another type and its length exceeds the specified size, the conversion will fail and return an Out of range error.
* When the target type is CHAR or NCHAR and its size is not specified, there is no need to consider the data type of expr, as the default estimated size for CHAR/NCHAR is 1.
* When the target type is VARCHAR and its size is not specified, the size will be estimated based on the type of expr. For string types, the estimated size is the length of the original string; for other types, the estimated size is the length of that type converted to a string.
* When the target type is NVARCHAR, its size must be specified, otherwise an error will be returned.

**charset_info**

This syntax is only supported when type_name is CHAR or VARCHAR. In the absence of the charset_info clause, CHAR or VARCHAR strings are generated using the default character set. If you need to explicitly specify the character set, you can use the following charset_info values:

* CHARACTER SET charset_name: Generates a string using the specified character set, where charset_name supports the same range of character sets as in mysql mode.

* ASCII: Abbreviation for CHARACTER SET latin1.

**transcoding_name**

Specifies the target character set to convert to, and the supported character sets are the same as those in mysql mode.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT CONVERT('abcd' ,CHAR(6)) res;

res  
------------------------- 
abcd                     

SELECT HEX(CONVERT('——' ,CHAR(9) ascii)) res;
res
----------------------------------
2D2D2D2D                                   

SELECT HEX(CONVERT('——' USING utf8)) res;
res
------------------------------
E28094E28094
                     
SELECT HEX(CONVERT('——' ,CHAR(9) CHARACTER SET gbk)) res;
res
----------------------------------------------
A1AAA1AA         
```

This view displays the parameter information of stored procedures and functions in the database, as well as the return values of stored functions.

|Field |Type |Description |
| --- | --- | --- |
| SPECIFIC_CATALOG         | varchar(3)   | The catalog name to which the stored procedure or user-defined function containing this parameter belongs. The parameter value is fixed as `def`. |
| SPECIFIC_SCHEMA          | varchar(64)  | The database instance to which the stored procedure or user-defined function containing this parameter belongs. |
| SPECIFIC_NAME            | varchar(532) | The name of the stored procedure or user-defined function containing this parameter. |
| ORDINAL_POSITION         | varchar(11)  | The position of the parameter in the stored procedure or user-defined function.<br>For stored procedure parameters, the values are 1, 2, 3, and so on.<br>For user-defined function parameters, the value 0 indicates the output parameter. |
| PARAMETER_MODE           | varchar(6)   | The input/output mode of the parameter. Parameter values are IN, OUT or INOUT.<br>For the return value of a user-defined function, this parameter value is fixed as `NULL`. |
| PARAMETER_NAME           | varchar(68)  | The parameter name.<br>For the return value of a user-defined function, this parameter value is fixed as `NULL`. |
| DATA_TYPE                | varchar(64)  | The data type of the parameter. |
| CHARACTER_MAXIMUM_LENGTH | int          | The character length of string-type parameters. |
| CHARACTER_OCTET_LENGTH   | varchar(20)  | The byte length of string-type parameters.<br>Syntax compatible only, no actual functional meaning. |
| NUMERIC_PRECISION        | int          | The precision of numeric-type parameters. |
| NUMERIC_SCALE            | int          | The decimal places of numeric-type parameters. |
| DATETIME_PRECISION       | varchar(20)  | The millisecond precision of time-type parameters.<br>Syntax compatible only, no actual functional meaning. |
| CHARACTER_SET_NAME       | varchar(20)  | The character set of string-type parameters.<br>Syntax compatible only, no actual functional meaning. |
| COLLATION_NAME           | varchar(20)  | The collation rule of string-type parameters.<br>Syntax compatible only, no actual functional meaning. |
| DTD_IDENTIFIER           | varchar(20)  | Detailed information about the parameter data type.<br>Syntax compatible only, no actual functional meaning. |
| ROUTINE_TYPE             | varchar(9)   | The object type to which this parameter belongs. Stored procedures are `PROCEDURE`, and user-defined functions are `FUNCTION`. |
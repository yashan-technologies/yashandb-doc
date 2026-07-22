The ODBC officially specifies the following [scalar functions](https://learn.microsoft.com/zh-cn/sql/odbc/reference/appendixes/appendix-scalar-functions?view=sql-server-ver16):

- String functions
- Numeric functions
- Date, time, and interval functions
- System functions
- Display data type conversion number
- SQL-92 CAST function

The scalar functions supported by the YashanDB ODBC driver are shown in the table below:

|Interface |Description |
| ----------------------------------- | ----------------------------------------------------------------------------- | 
| CONCAT(string_exp1, string_exp2)  | Concatenates the string string_exp2 to string_exp1 and returns it. |
| IFNULL(exp, value) | Returns value if *exp* is null. If *exp* is not null, it returns *exp*.<br/>The data type or value type must be compatible with the data type of *exp*. |
| LEFT(string_exp, count) | Returns the first count characters from left to right. |
| SUBSTRING(string_exp, start, length) | Returns the substring extracted from string_exp, starting at the start position for a length of length. |
| RIGHT(string_exp, count) | Returns the first count characters from right to left. |
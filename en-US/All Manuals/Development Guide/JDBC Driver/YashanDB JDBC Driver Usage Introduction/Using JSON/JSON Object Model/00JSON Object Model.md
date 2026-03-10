The com.yashandb.json package defines the meanings and corresponding relationships of the 16 types of object models currently supported by YashanDB for JSON:

|YasonType |code |JSON Object Model |Meaning |
|-----------------|------|-------------|----------------------------------------------|
| YASON_OBJECT    | 1    | YasonObject       | Standard JSON type, representing the Object type of JSON.            |
| YASON_ARRAY     | 2    | YasonArray        | Standard JSON type, representing the Array type of JSON.             |
| YASON_STRING    | 3    | YasonString       | Standard JSON type, representing the String type of JSON.            |
| YASON_NUMBER    | 4    | YasonDecimal      | Standard JSON type, representing the Number type of JSON.            |
| YASON_BOOLEAN   | 5    | YasonBoolean      | Standard JSON type, representing the Boolean type of JSON.           |
| YASON_NULL      | 6    | YasonNull         | Standard JSON type, representing the null type of JSON.              |
| YASON_BYTE      | 7    | YasonByte         | Extended JSON type, a subdivision of the Number type, corresponding to integer types in the range of -128 to 127. |
| YASON_SHORT     | 8    | YasonShort        | Extended JSON type, a subdivision of the Number type, corresponding to the Java short type. |
| YASON_INT       | 9    | YasonInt          | Extended JSON type, a subdivision of the Number type, corresponding to the Java int type.   |
| YASON_BIGINT    | 10   | YasonLong         | Extended JSON type, a subdivision of the Number type, corresponding to the Java long type.  |
| YASON_FLOAT     | 11   | YasonFloat        | Extended JSON type, a subdivision of the Number type, corresponding to the Java float type.  |
| YASON_DOUBLE    | 12   | YasonDouble       | Extended JSON type, a subdivision of the Number type, corresponding to the Java double type. |
| YASON_BINARY    | 13   | YasonBinary       | Extended JSON type, representing binary type, corresponding to byte arrays in Java.          |
| YASON_TIMESTAMP | 14   | YasonTimestamp    | Extended JSON type, corresponding to the Timestamp type in Java.   |
| YASON_DATE      | 15   | YasonDate         | Extended JSON type, corresponding to the Date type in Java.         |
| YASON_TIME      | 16   | YasonTime         | Extended JSON type, corresponding to the Time type in Java.         |

Note: Types 3 to 16 are non-scalar types; their contained values cannot be modified once created.

All 16 object models inherit from the YasonValue parent interface, which includes the following methods:

|Return Type |Method |Remarks |
|-----------|-------------------------------------|------------------------|
| YasonType   | getType()                          | Get the JSON type.              |
| int         | getDepth() throws SQLException     | Get the JSON depth; exception is thrown if depth exceeds 100. |
| Object      | getValue()                         | Get the actual internal value of JSON.  |
| String      | getString()                        | Get the string representation of the JSON value. |
| byte[]      | getBinaryData() throws SQLException | Get the binary data encoded from JSON. |
| String      | toString()                         | Get the JSON formatted string.   |
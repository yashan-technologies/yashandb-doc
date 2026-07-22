Currently, YashanDB ODBC supports two character sets: ANSI character set and UTF16 character set. The use of different character sets mainly affects the **string data types** and **function calls**.

Applications can be compiled as UNICODE applications in the following ways; otherwise, they will default to being compiled as ANSI applications:

- Add the Unicode #define in the application's Sqlucode.h header file.

- Compile the application using the UNICODE option of the compiler. The name and method of this option may vary slightly among different compilers; please refer to the actual documentation.

## Application Character Set

- ANSI Application Character Set

    - Windows: The character set of the ANSI driver interface is the system's default code page character set. To modify the ANSI driver interface character set, simply change the code page character set.

    - Linux: UTF8 is used as the default ANSI driver interface character set. To set it to another character set, this can be done through the CHARACTER_SET field in the custom data source.

- UNICODE Application Character Set

    Both Windows and Linux use UTF16 character set as the UNICODE driver interface character set.

## UNICODE Function

The currently supported UNICODE functions are as follows:

|Interface |Length Count Unit |
| ------------------ | ----------- | 
| SQLColAttributeW    | Bytes             |
| SQLConnectW         | Characters        |
| SQLColumnsW         | Characters        |
| SQLDescribeColW     | Characters        |
| SQLDriverConnectW    | Characters        |
| SQLExecDirectW      | Characters        |
| SQLGetConnectAttrW   | Bytes             |
| SQLGetDescFieldW     | Bytes             |
| SQLGetDiagFieldW     | Bytes             |
| SQLGetDiagRecW      | Characters        |
| SQLGetInfoW         | Bytes             |
| SQLGetStmtAttrW      | Bytes             |
| SQLGetTypeInfoW     | Irrelevant        |
| SQLPrepareW         | Characters        |
| SQLSetConnectAttrW   | Bytes             |
| SQLSetDescFieldW     | Bytes             |
| SQLSetStmtAttrW      | Bytes             |
| SQLTablesW          | Characters        |

## UNICODE Data Type

The currently supported UNICODE C data types are as follows:

|Type |Length Count Unit |
| ----------- |  ---------------------------------- |
| SQL_C_WCHAR | Bytes              |
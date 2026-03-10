目前YashanDB ODBC支持两种字符集：ANSI字符集和UTF16字符集，使用不同的字符集主要会影响**字符串数据类型**和**函数调用**。

通过以下方式可以将应用程序编译为UNICODE应用程序，其余情况则将默认编译为ANSI应用程序：

- 在应用程序中的Sqlucode.h头文件中新增的Unicode #define。

- 使用编译器的UNICODE选项编译应用程序，不同编译器中该选项名称及设置方法略有不同，具体以实际为准。

## 应用程序字符集

- ANSI应用程序字符集

    - Windows：使用操作系统默认代码页字符集作为ANSI驱动接口字符集。如需修改ANSI驱动接口字符集，直接修改代码页字符集即可。

    - Linux：使用UTF8作为默认ANSI驱动接口字符集。如需设置为其他字符集，可通过自定义数据源中的CHARACTER_SET字段来实现。

- UNICODE应用程序字符集

    Windows和Linux均使用UTF16字符集作为UNICODE驱动接口字符集。

## UNICODE函数

目前已支持的UNICODE函数如下：

| 接口 |  长度计数单位 |
| ------------------ | ----------- | 
| SQLColAttributeW |  字节 |
| SQLConnectW |  字符 |
| SQLColumnsW |  字符 |
| SQLDescribeColW |  字符 |
| SQLDriverConnectW |  字符 |
| SQLExecDirectW |  字符 |
| SQLGetConnectAttrW |  字节 |
| SQLGetDescFieldW |  字节 |
| SQLGetDiagFieldW |  字节 |
| SQLGetDiagRecW |  字符 |
| SQLGetInfoW |  字节 |
| SQLGetStmtAttrW |  字节 |
| SQLGetTypeInfoW |  无关 |
| SQLPrepareW |  字符 |
| SQLSetConnectAttrW |  字节 |
| SQLSetDescFieldW |  字节 |
| SQLSetStmtAttrW |  字节 |
| SQLTablesW |  字符 |

## UNICODE数据类型

目前已支持的UNICODE C数据类型如下：

| 类型        |  长度计数单位 |
| ----------- |  ---------------------------------- |
| SQL_C_WCHAR | 字节                               |

## Standard Data Type Description

ODBC defines two sets of standard data types:

- SQL Data Types: SQL data types indicate the data type of the data stored in the data source, and the storage structure is invisible to the user (the user does not need to manage its memory).
- C Data Types: C data types indicate the data type of the data stored in the application cache, and the storage structure is visible to the user (the user needs to manage its memory).

The similarities and differences between the two data types are shown in the table below:

|Similarities and Differences |SQL Data Types |C Data Types |
| ------------------ | ----------- | --------- |
| Enum Prefix                  | SQL_XXX        | SQL_C_XXX    |
| Same across different databases| No             | Yes          |
| Storage Structure Visibility  | Invisible       | Visible      |
| Buffer Allocation             | Driver          | User         |
| Buffer Filling                | Driver          | Driver       |
| Buffer Reading                | Driver          | User         |

For more detailed explanations, please refer to the following official documents:

- [SQL Data Types - ODBC API Reference | Microsoft Docs](https://docs.microsoft.com/zh-cn/sql/odbc/reference/appendixes/sql-data-types?view=sql-server-ver15)
- [C Data Types - ODBC API Reference | Microsoft Docs](https://docs.microsoft.com/zh-cn/sql/odbc/reference/appendixes/c-data-types?view=sql-server-ver15)
- [Converting Data from SQL to C Data Types - ODBC API Reference | Microsoft Docs](https://docs.microsoft.com/zh-cn/sql/odbc/reference/appendixes/converting-data-from-sql-to-c-data-types?view=sql-server-ver15)
- [Converting Data from C to SQL Data Types - ODBC API Reference | Microsoft Docs](https://docs.microsoft.com/zh-cn/sql/odbc/reference/appendixes/converting-data-from-c-to-sql-data-types?view=sql-server-ver15)

In the usage of the YashanDB ODBC driver, the main focus is on the mutual conversion between SQL data types and C data types, with a common usage scenario being SQL data types → C data types (fetch).

## SQL Data Type Support Status

Note: Data types marked with * are newly added types specific to YashanDB and are not specified in the standard ODBC documentation.

|SQL Data Types |Value |YashanDB Support Status |
| ----------------------------- | ---- | ---------------- |
| SQL_CHAR                      | 1    | √                |
| SQL_VARCHAR                   | 12   | √                |
| SQL_LONGVARCHAR               |      | ×                |
| SQL_WCHAR                     |      | ×                |
| SQL_WVARCHAR                  |      | ×                |
| SQL_WLONGVARCHAR              |      | ×                |
| SQL_DECIMAL                   |      | ×                |
| SQL_NUMERIC                   | 2    | √                |
| SQL_SMALLINT                  | 5    | √                |
| SQL_INTEGER                   | 4    | √                |
| SQL_REAL                      |      | ×                |
| SQL_FLOAT                     | 6    | √                |
| SQL_DOUBLE                    | 8    | √                |
| SQL_BIT                       | -7   | √                |
| SQL_TINYINT                   | -6   | √                |
| SQL_BIGINT                    | -5   | √                |
| SQL_BINARY                    | -2   | √                |
| SQL_VARBINARY                 |      | ×                |
| SQL_LONGVARBINARY             |      | ×                |
| SQL_TYPE_DATE                 | 91   | √                |
| SQL_TYPE_TIME                 | 92   | √                |
| SQL_TYPE_TIMESTAMP            | 93   | √                |
| SQL_TYPE_UTCDATETIME          |      | ×                |
| SQL_TYPE_UTCTIME              |      | ×                |
| SQL_INTERVAL_MONTH            |      | ×                |
| SQL_INTERVAL_YEAR             |      | ×                |
| SQL_INTERVAL_YEAR_TO_MONTH    | 107  | √                |
| SQL_INTERVAL_DAY              |      | ×                |
| SQL_INTERVAL_HOUR             |      | ×                |
| SQL_INTERVAL_MINUTE           |      | ×                |
| SQL_INTERVAL_SECOND           |      | ×                |
| SQL_INTERVAL_DAY_TO_HOUR      |      | ×                |
| SQL_INTERVAL_DAY_TO_MINUTE    |      | ×                |
| SQL_INTERVAL_DAY_TO_SECOND    | 110  | √                |
| SQL_INTERVAL_HOUR_TO_MINUTE   |      | ×                |
| SQL_INTERVAL_HOUR_TO_SECOND   |      | ×                |
| SQL_INTERVAL_MINUTE_TO_SECOND |      | ×                |
| SQL_GUID                      |      | √                |
| SQL_BLOB*                     |      | ×                |
| SQL_CLOB*                     |      | ×                |
| SQL_BOOLEAN*                  |      | ×                |

> **Note**: 
> SQL_GUID currently only supports binding input parameters and does not support binding output parameters or fetch.

## C Data Type Support Status

|C Type Identifier |ODBC C typedef |C Type |YashanDB Support Status |
| :--------------------------- | :------------------- | :----------------------------------------------------------- | ---------------- |
| SQL_C_CHAR                   | SQLCHAR *            | unsigned char *                                              | √                |
| SQL_C_WCHAR                  | SQLWCHAR *           | wchar_t *                                                    | ×                |
| SQL_C_SSHORT[j]              | SQLSMALLINT          | short int                                                    | √                |
| SQL_C_USHORT[j]              | SQLUSMALLINT         | unsigned short int                                           | √                |
| SQL_C_SLONG[j]               | SQLINTEGER           | long int                                                     | √                |
| SQL_C_ULONG[j]               | SQLUINTEGER          | unsigned long int                                            | √                |
| SQL_C_FLOAT                  | SQLREAL              | float                                                        | √                |
| SQL_C_DOUBLE                 | SQLDOUBLE, SQLFLOAT  | double                                                       | √                |
| SQL_C_BIT                    | SQLCHAR              | unsigned char                                                | √                |
| SQL_C_STINYINT[j]            | SQLSCHAR             | signed char                                                  | √                |
| SQL_C_UTINYINT[j]            | SQLCHAR              | unsigned char                                                | √                |
| SQL_C_SBIGINT                | SQLBIGINT            | _int64[h]                                                    | √                |
| SQL_C_UBIGINT                | SQLUBIGINT           | unsigned _int64[h]                                           | √                |
| SQL_C_BINARY                 | SQLCHAR *            | unsigned char *                                              | √                |
| SQL_C_BOOKMARK[i]            | Bookmark              | unsigned long int[d]                                       |                       |
| SQL_C_VARBOOKMARK            | SQLCHAR *            | unsigned char *                                              |                  |
| SQL_C_TYPE_DATE[c]           | SQL_DATE_STRUCT      | struct tagDATE_STRUCT {  SQLSMALLINT year;  SQLUSMALLINT month;  SQLUSMALLINT day;  } DATE_STRUCT;[a] | √                |
| SQL_C_TYPE_TIME[c]           | SQL_TIME_STRUCT      | struct tagTIME_STRUCT {  SQLUSMALLINT hour;  SQLUSMALLINT minute;  SQLUSMALLINT second;  } TIME_STRUCT;[a] | √                |
| SQL_C_TYPE_TIMESTAMP[c]      | SQL_TIMESTAMP_STRUCT | struct tagTIMESTAMP_STRUCT {  SQLSMALLINT year;  SQLUSMALLINT month;  SQLUSMALLINT day;  SQLUSMALLINT hour;  SQLUSMALLINT minute;  SQLUSMALLINT second;  SQLUINTEGER fraction;[b]  } TIMESTAMP_STRUCT;[a] | √                |
| SQL_C_NUMERIC                | SQL_NUMERIC_STRUCT   | struct tagSQL_NUMERIC_STRUCT {  SQLCHAR precision;  SQLSCHAR scale;  SQLCHAR sign[g];  SQLCHAR val[SQL_MAX_NUMERIC_LEN];[e], [f]  } SQL_NUMERIC_STRUCT; | √                |
| SQL_C_GUID                   | SQLGUID              | struct tagSQLGUID {  DWORD Data1;  WORD Data2;  WORD Data3;  BYTE Data4[8];  } SQLGUID;[k] | √                |
| SQL_C_INTERVAL_YEAR_TO_MONTH | SQL_INTERVAL_STRUCT  | struct tagSQL_INTERVAL_STRUCT { SQLINTERVAL interval_type; SQLSMALLINT interval_sign; union { SQL_YEAR_MONTH_STRUCT year_month; SQL_DAY_SECOND_STRUCT day_second; } intval; } SQL_INTERVAL_STRUCT; | √                |
| SQL_C_INTERVAL_DAY_TO_SECOND | SQL_INTERVAL_STRUCT  | struct tagSQL_INTERVAL_STRUCT { SQLINTERVAL interval_type; SQLSMALLINT interval_sign; union { SQL_YEAR_MONTH_STRUCT year_month; SQL_DAY_SECOND_STRUCT day_second; } intval; } SQL_INTERVAL_STRUCT; | √                |

> **Note**: 
> SQL_C_GUID currently only supports binding input parameters and does not support binding output parameters or fetch.
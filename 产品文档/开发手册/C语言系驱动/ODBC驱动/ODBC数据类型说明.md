## 标准数据类型说明

ODBC定义了两个标准的数据类型集：

- SQL数据类型：SQL数据类型指示存储在数据源中的数据的数据类型， 存储结构对用户不可见 （用户无需管理其内存）。
- C数据类型：C数据类型指示存储在应用程序缓冲区中的数据的数据类型， 存储结构对用户可见 （用户需要管理其内存）。

两种数据类型的异同见下表：

| 异同               | SQL数据类型 | C数据类型 |
| ------------------ | ----------- | --------- |
| enum前缀           | SQL_XXX     | SQL_C_XXX |
| 不同数据库是否相同 | 不同        | 相同      |
| 存储结构可见性     | 不可见      | 可见      |
| buffer申请         | 驱动        | 用户      |
| buffer填充         | 驱动        | 驱动      |
| buffer读           | 驱动        | 用户      |

更详细的解释请参考如下官方文档：

- [SQL数据类型 - ODBC API Reference | Microsoft Docs](https://docs.microsoft.com/zh-cn/sql/odbc/reference/appendixes/sql-data-types?view=sql-server-ver15)
- [C 数据类型 - ODBC API Reference | Microsoft Docs](https://docs.microsoft.com/zh-cn/sql/odbc/reference/appendixes/c-data-types?view=sql-server-ver15)
- [将数据从 SQL 转换为 C 数据类型 - ODBC API Reference | Microsoft Docs](https://docs.microsoft.com/zh-cn/sql/odbc/reference/appendixes/converting-data-from-sql-to-c-data-types?view=sql-server-ver15) 
- [将数据从 C 转换为 SQL 数据类型 - ODBC API Reference | Microsoft Docs](https://docs.microsoft.com/zh-cn/sql/odbc/reference/appendixes/converting-data-from-c-to-sql-data-types?view=sql-server-ver15) 

在YashanDB ODBC驱动的使用中，主要需要关注的是SQL数据类型和C数据类型的相互转换，常见使用场景为SQL数据类型→C数据类型（fetch）。

## SQL数据类型支持情况

注：标*的数据类型在标准ODBC文档未规定，为YashanDB新增类型。

| SQL数据类型                   | 数值 | YashanDB支持情况 |
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

> **Note**：
> SQL_GUID目前只支持绑定入参，不支持绑定出参和fetch。

## C数据类型支持情况

| C 类型标识符                 | ODBC C typedef       | C 类型                                                       | YashanDB支持情况 |
| :--------------------------- | :------------------- | :----------------------------------------------------------- | ---------------- |
| SQL_C_CHAR                   | SQLCHAR *            | unsigned char *                                              | √                |
| SQL_C_WCHAR                  | SQLWCHAR *           | wchar_t *                                                    | ×                |
| SQL_C_SSHORT[j]              | SQLSMALLINT          | short int                                                    | √                |
| SQL_C_USHORT[j]              | SQLUSMALLINT         | unsigned short int                                           | √                |
| SQL_C_SLONG[j]               | SQLINTEGER           | long int                                                     | √                |
| SQL_C_ULONG[j]               | SQLUINTEGER          | unsigned long int                                            | √                |
| SQL_C_FLOAT                  | SQLREAL              | float                                                        | √                |
| SQL_C_DOUBLE                 | SQLDOUBLE、SQLFLOAT  | double                                                       | √                |
| SQL_C_BIT                    | SQLCHAR              | unsigned char                                                | √                |
| SQL_C_STINYINT[j]            | SQLSCHAR             | signed char                                                  | √                |
| SQL_C_UTINYINT[j]            | SQLCHAR              | unsigned char                                                | √                |
| SQL_C_SBIGINT                | SQLBIGINT            | _int64[h]                                                    | √                |
| SQL_C_UBIGINT                | SQLUBIGINT           | unsigned _int64[h]                                           | √                |
| SQL_C_BINARY                 | SQLCHAR *            | unsigned char *                                              | √                |
| SQL_C_BOOKMARK[i]            | 书签                 | unsigned long int[d]                                         |                  |
| SQL_C_VARBOOKMARK            | SQLCHAR *            | unsigned char *                                              |                  |
| SQL_C_TYPE_DATE[c]           | SQL_DATE_STRUCT      | struct tagDATE_STRUCT {  SQLSMALLINT year;  SQLUSMALLINT month;  SQLUSMALLINT day;  } DATE_STRUCT;[a] | √                |
| SQL_C_TYPE_TIME[c]           | SQL_TIME_STRUCT      | struct tagTIME_STRUCT {  SQLUSMALLINT hour;  SQLUSMALLINT minute;  SQLUSMALLINT second;  } TIME_STRUCT;[a] | √                |
| SQL_C_TYPE_TIMESTAMP[c]      | SQL_TIMESTAMP_STRUCT | struct tagTIMESTAMP_STRUCT {  SQLSMALLINT year;  SQLUSMALLINT month;  SQLUSMALLINT day;  SQLUSMALLINT hour;  SQLUSMALLINT minute;  SQLUSMALLINT second;  SQLUINTEGER fraction;[b]  } TIMESTAMP_STRUCT;[a] | √                |
| SQL_C_NUMERIC                | SQL_NUMERIC_STRUCT   | struct tagSQL_NUMERIC_STRUCT {  SQLCHAR precision;  SQLSCHAR scale;  SQLCHAR sign[g];  SQLCHAR val[SQL_MAX_NUMERIC_LEN];[e], [f]  } SQL_NUMERIC_STRUCT; | √                |
| SQL_C_GUID                   | SQLGUID              | struct tagSQLGUID {  DWORD Data1;  WORD Data2;  WORD Data3;  BYTE Data4[8];  } SQLGUID;[k] | √                |
| SQL_C_INTERVAL_YEAR_TO_MONTH | SQL_INTERVAL_STRUCT  | struct tagSQL_INTERVAL_STRUCT { SQLINTERVAL interval_type; SQLSMALLINT interval_sign; union { SQL_YEAR_MONTH_STRUCT year_month; SQL_DAY_SECOND_STRUCT day_second; } intval; } SQL_INTERVAL_STRUCT; | √                |
| SQL_C_INTERVAL_DAY_TO_SECOND | SQL_INTERVAL_STRUCT  | struct tagSQL_INTERVAL_STRUCT { SQLINTERVAL interval_type; SQLSMALLINT interval_sign; union { SQL_YEAR_MONTH_STRUCT year_month; SQL_DAY_SECOND_STRUCT day_second; } intval; } SQL_INTERVAL_STRUCT; | √                |

> **Note**：
> SQL_C_GUID目前只支持绑定入参，不支持绑定出参和fetch。
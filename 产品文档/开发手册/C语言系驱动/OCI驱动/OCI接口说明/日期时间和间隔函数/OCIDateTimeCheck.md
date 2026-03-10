## 功能简介

OCIDateTimeCheck函数用于检查给定日期是否有效。

## 函数声明

```c
sword OCIDateTimeCheck( void              *hndl, 
                        OCIError          *err, 
                        const OCIDateTime *date, 
                        ub4               *valid );
```

## 参数说明

| 参数名           | 说明                                                                                              |
|---------------|-------------------------------------------------------------------------------------------------|
| hndl (IN)     | 用户会话句柄或环境句柄。                                                                                    |
| err (IN/OUT)  | 错误句柄。<br />如果出现错误，则记录在err中，此函数返回OCI_error。<br />通过调用[OCIErrorGet](../辅助功能函数/OCIErrorGet)获取诊断信息。 |
| date (IN)     | 指向OCIDateTime描述符的指针，从该描述符中检索日期信息。                                                               |
| valid (OUT)   | 如果是有效日期，返回0；否则，返回指定的所有错误位的逻辑运算符OR组合。                                                            |

错误位参数：

| 宏名称                          | 位值     | 错误信息                                  |
|------------------------------|--------|---------------------------------------|
| OCI_DT_INVALID_DAY           | 0x1    | Bad day                               |
| OCI_DT_DAY_BELOW_VALID       | 0x2    | Bad day low bit (1=low)               |
| OCI_DT_INVALID_MONTH         | 0x4    | Bad month                             |
| OCI_DT_MONTH_BELOW_VALID     | 0x8    | Bad month low bit (1=low)             |
| OCI_DT_INVALID_YEAR          | 0x10   | Bad year                              |
| OCI_DT_YEAR_BELOW_VALID      | 0x20   | Bad year low bit (1=low)              |
| OCI_DT_INVALID_HOUR          | 0x40   | Bad hour                              |
| OCI_DT_HOUR_BELOW_VALID      | 0x80   | Bad hour low bit (1=low)              |
| OCI_DT_INVALID_MINUTE        | 0x100  | Bad minute                            |
| OCI_DT_MINUTE_BELOW_VALID    | 0x200  | Bad minute low bit (1=low)            |
| OCI_DT_INVALID_SECOND        | 0x400  | Bad second                            |
| OCI_DT_SECOND_BELOW_VALID    | 0x800  | Bad second low bit (1=low)            |
| OCI_DT_DAY_MISSING_FROM_1582 | 0x1000 | Day is one of those missing from 1582 |
| OCI_DT_YEAR_ZERO             | 0x2000 | Year may not equal zero               |
| OCI_DT_INVALID_TIMEZONE      | 0x4000 | Bad time zone                         |
| OCI_DT_INVALID_FORMAT        | 0x8000 | Bad date format input                 |
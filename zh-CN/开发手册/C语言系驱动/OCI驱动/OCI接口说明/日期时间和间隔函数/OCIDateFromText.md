## 功能简介

OCIDateFromText函数用于解析日期类型字符串，返回值date即为字符串对应的日期类型。

## 函数声明

```c
sword OCIDateFromText( OCIError      *err, 
                       const oratext *date_str, 
                       ub4           d_str_length, 
                       const oratext *fmt, 
                       ub1           fmt_length, 
                       const oratext *lang_name, 
                       ub4           lang_length, 
                       OCIDate       *date );
```

## 参数说明

|  参数名| 说明|
|-------------------|-----------------------------------------------------------------------------------------------|
| err (IN/OUT)      | 错误句柄。<br />如果出现错误，则记录在err中，此函数返回OCI_error。<br />通过调用[OCIErrorGet](../辅助功能函数/OCIErrorGet)获取诊断信息。 |
| date_str (IN)     | 时间字符串，从该字符串中解析出日期信息。                                                                       |
| d_str_length (IN) | 时间字符串参数长度。                                                                                    |
| fmt (IN)          | 时间字符串格式，例如YYYY-MM-DD。                                                                         |
| fmt_length (IN)   | 时间字符串格式参数长度。                                                                                  |
| lang_name (IN)    | 支持返回日期的语言，仅支持EN。                                                                              |
| lang_length (IN)  | 返回日期的语言参数长度。                                                                                  |
| date (OUT)        | 解析出的日期。                                                                                       |

## 功能简介

OCIDateTimeFromText函数用于解析时间类型字符串，返回值date即为字符串对应的时间类型。

## 函数声明

```c
sword OCIDateTimeFromText( void          *hndl, 
                           OCIError      *err, 
                           const OraText *date_str,
                           size_t        dstr_length, 
                           const OraText *fmt, 
                           ub1           fmt_length,
                           const OraText *lang_name, 
                           size_t        lang_length, 
                           OCIDateTime   *date );
```

## 参数说明

| 参数名              | 说明                                                                                            |
|------------------|-----------------------------------------------------------------------------------------------|
| hndl (IN)        | 用户会话句柄或环境句柄。                                                                                  |
| err (IN/OUT)     | 错误句柄。<br />如果出现错误，则记录在err中，此函数返回OCI_error。<br />通过调用[OCIErrorGet](../辅助功能函数/OCIErrorGet)获取诊断信息。 |
| date_str (IN)    | 时间类型字符串。                                                                                   |
| dstr_length (IN) | 时间类型字符串参数长度。                                                                                  |
| fmt (IN)         | 时间字符串格式，例如YYYY-MM-DD HH24:MI:SS.FF。                                                           |
| fmt_length (IN)  | 时间字符串格式参数长度。                                                                                  |
| lang_name (IN)   | 支持返回日期的语言，仅支持EN。                                                                              |
| lang_length (IN) | 返回日期的语言参数长度。                                                                                  |
| date (OUT)       | 解析出的时间。                                                                                       |

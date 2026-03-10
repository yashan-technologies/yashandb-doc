## 功能简介

OCIDateToText函数用于将一个日期类型转换成一个日期字符串。

## 函数声明

```c
sword OCIDateToText( OCIError      *err, 
                     const OCIDate *date,
                     const oratext *fmt, 
                     ub1           fmt_length,
                     const oratext *lang_name, 
                     ub4           lang_length,
                     ub4           *buf_size, 
                     oratext       *buf    );
```

## 参数说明

|  参数名| 说明|
|-------------------|-------------------------------------------------------------------------------------------------|
| hndl (IN)         | 用户会话句柄或环境句柄。                                                                                    |
| err (IN/OUT)      | 错误句柄。<br />如果出现错误，则记录在err中，此函数返回OCI_error。<br />通过调用[OCIErrorGet](../辅助功能函数/OCIErrorGet)获取诊断信息。 |
| date (IN)         | 指向OCIDate描述符的指针，从该描述符中检索日期信息。                                                                   |
| fmt (IN)          | 时间字符串格式，例如YYYY-MM-DD。                                                                           |
| fmt_length (IN)   | 时间字符串格式参数长度。                                                                                    |
| lang_name (IN)    | 支持返回日期的语言，仅支持EN。                                                                                |
| lang_length (IN)  | 返回日期的语言参数长度。                                                                                    |
| buf_size (IN/OUT) | 指定buf最大长度，并返回buf实际长度。                                                                           |
| buf (OUT)         | 转换后的时间字符串。                                                                                      |

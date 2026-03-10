## 功能简介

OCIDateTimeGetTimeZoneOffset函数用于获取当前时间类型的时区。

## 函数声明

```c
sword OCIDateTimeGetTimeZoneOffset( void              *hndl,
                                    OCIError          *err,
                                    const OCIDateTime *datetime,
                                    sb1               *hr,
                                    sb1               *mm);
```

## 参数说明

| 参数名           | 说明                                                                                              |
|---------------|-------------------------------------------------------------------------------------------------|
| hndl (IN)     | 用户会话句柄或环境句柄。                                                                                    |
| err (IN/OUT)  | 错误句柄。<br />如果出现错误，则记录在err中，此函数返回OCI_error。<br />通过调用[OCIErrorGet](../辅助功能函数/OCIErrorGet)获取诊断信息。 |
| datetime (IN) | 指向OCIDateTime描述符的指针，从该描述符中检索日期信息。                                                               |
| hr (OUT)      | 时区的时偏移量。                                                                                        |
| mm (OUT)      | 时区的分钟偏移量。                                                                                       |

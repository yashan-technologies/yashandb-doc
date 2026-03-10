## 功能简介

OCIDateTimeConvert函数用于将一个时间类型转换成另一个时间类型。

## 函数声明

```c
sword OCIDateTimeConvert( void        *hndl, 
                          OCIError    *err, 
                          OCIDateTime *indate,
                          OCIDateTime *outdate);
```

## 参数说明

|  参数名| 说明|
|---------------|-------------------------------------------------------------------------------------------------|
| hndl (IN)     | 用户会话句柄或环境句柄。                                                                                    |
| err (IN/OUT)  | 错误句柄。<br />如果出现错误，则记录在err中，此函数返回OCI_error。<br />通过调用[OCIErrorGet](../辅助功能函数/OCIErrorGet)获取诊断信息。 |
| indate (IN)   | 指向OCIDateTime描述符的指针，从该描述符中检索日期信息。                                                               |
| outdate (OUT) | 指向OCIDateTime描述符的指针，保存转换后的日期信息。                                                                 |

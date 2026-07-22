## 功能简介

OCIDateTimeSysTimeStamp函数用于当前客户端时间戳。

## 函数声明

```c
sword OCIDateTimeSysTimeStamp( void        *hndl, 
                               OCIError    *err,
                               OCIDateTime *sys_date );
```

## 参数说明

|  参数名| 说明|
|----------------|-------------------------------------------------------------------------------------------------|
| hndl (IN)      | 用户会话句柄或环境句柄。                                                                                    |
| err (IN/OUT)   | 错误句柄。<br />如果出现错误，则记录在err中，此函数返回OCI_error。<br />通过调用[OCIErrorGet](../辅助功能函数/OCIErrorGet)获取诊断信息。 |
| datetime (OUT) | 指向OCIDateTime描述符的指针，检索出的日期信息。                                                                   |

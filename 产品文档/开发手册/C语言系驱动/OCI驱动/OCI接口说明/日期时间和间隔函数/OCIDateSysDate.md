## 功能简介

OCIDateSysDate函数用于获取当前客户端时间。

## 函数声明

```c
sword OCIDateSysDate( OCIError *err, 
                      OCIDate  *sys_date );
```

## 参数说明

| 参数名              | 说明                                                                                              |
|------------------|-------------------------------------------------------------------------------------------------|
| err (IN/OUT)     | 错误句柄。<br />如果出现错误，则记录在err中，此函数返回OCI_error。<br />通过调用[OCIErrorGet](../辅助功能函数/OCIErrorGet)获取诊断信息。 |
| sys_date (OUT)   | 获取到的时间。                                                                                         |


## 功能简介

OCIDateTimeAssign函数用于分配一个时间类型。

## 函数声明

```c
sword OCIDateTimeAssign(void              *hndl, 
                        OCIError          *err, 
                        const OCIDateTime *from, 
                        OCIDateTime       *to);
```

## 参数说明

|  参数名| 说明|
|--------------|-------------------------------------------------------------------------------------------------|
| hndl (IN)    | 用户会话句柄或环境句柄。                                                                                    |
| err (IN/OUT) | 错误句柄。<br />如果出现错误，则记录在err中，此函数返回OCI_error。<br />通过调用[OCIErrorGet](../辅助功能函数/OCIErrorGet)获取诊断信息。 |
| from (IN)    | 指向OCIDateTime描述符的指针，从该描述符中检索时间信息。                                                               |
| to (OUT)     | 分配出的时间。                                                                                         |

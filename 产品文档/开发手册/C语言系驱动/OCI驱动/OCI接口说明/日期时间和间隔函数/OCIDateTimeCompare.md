## 功能简介

OCIDateTimeCompare函数用于比较两个时间的大小。

## 函数声明

```c
sword OCIDateTimeCompare( void              *hndl, 
                          OCIError          *err, 
                          const OCIDateTime *date1,
                          const OCIDateTime *date2,  
                          sword             *result );
```

## 参数说明

| 参数名           | 说明                                                                                              |
|---------------|-------------------------------------------------------------------------------------------------|
| hndl (IN)     | 用户会话句柄或环境句柄。                                                                                    |
| err (IN/OUT)  | 错误句柄。<br />如果出现错误，则记录在err中，此函数返回OCI_error。<br />通过调用[OCIErrorGet](../辅助功能函数/OCIErrorGet)获取诊断信息。 |
| date1 (IN)    | 指向OCIDateTime描述符的指针，从该描述符中检索日期信息。                                                               |
| date2 (IN)    | 指向OCIDateTime描述符的指针，从该描述符中检索日期信息。                                                               |
| result (OUT)  | 比较结果。<br/>若date1 > date2，则返回1；若date1 = date2，则返回0；否则返回-1。                                       |

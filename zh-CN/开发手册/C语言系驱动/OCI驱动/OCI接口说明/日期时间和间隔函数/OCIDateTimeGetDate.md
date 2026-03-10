## 功能简介

OCIDateTimeGetDate函数用于获取日期（年、月、日）。

## 函数声明

```c
sword OCIDateTimeGetDate ( void               *hndl,
                           OCIError           *err, 
                           const OCIDateTime  *datetime,
                           sb2                *year, 
                           ub1                *month, 
                           ub1                *day );
```

## 参数说明

|  参数名| 说明|
| ------------- | ------------------------------------------------------------ |
| hndl (IN)     | 用户会话句柄或环境句柄。                                       |
| err (IN/OUT)  | 错误句柄。<br />如果出现错误，则记录在err中，此函数返回OCI_error。<br />通过调用[OCIErrorGet](../辅助功能函数/OCIErrorGet)获取诊断信息。 |
| datetime (IN) | 指向OCIDateTime描述符的指针，从该描述符中检索日期信息。      |
| year (OUT)    | 检索出的年份值。                                               |
| month (OUT)   | 检索出的月份值。                                               |
| day (OUT)     | 检索出的日值。                                                 |

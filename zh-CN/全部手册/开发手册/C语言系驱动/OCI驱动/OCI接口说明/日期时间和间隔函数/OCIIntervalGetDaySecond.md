## 功能简介

OCIIntervalGetDaySecond函数用于获取时间间隔中的日、小时、分钟和秒值。

## 函数声明

```c
sword OCIIntervalGetDaySecond (void               *hndl, 
                               OCIError           *err, 
                               sb4                *dy, 
                               sb4                *hr,
                               sb4                *mm, 
                               sb4                *ss, 
                               sb4                *fsec, 
                               const OCIInterval  *interval );
```

## 参数说明

|  参数名| 说明|
| ------------- | ------------------------------------------------------------ |
| hndl (IN)     | 用户会话句柄或环境句柄。                                       |
| err (IN/OUT)  | 错误句柄。<br />如果出现错误，则记录在err中，此函数返回OCI_error。<br />通过调用[OCIErrorGet](../辅助功能函数/OCIErrorGet)获取诊断信息。 |
| dy (OUT)      | 天数。                                                         |
| hr (OUT)      | 小时数。                                                       |
| mm (OUT)      | 分钟数。                                                       |
| ss (OUT)      | 秒数。                                                         |
| fsec (OUT)    | 纳秒数。                                                       |
| interval (IN) | 输入间隔。                                                     |

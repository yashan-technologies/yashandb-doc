## 功能简介

OCIDateTimeGetTime函数用于获取时间（小时、分钟、秒、小数秒）。

## 函数声明

```c
sword OCIDateTimeGetTime ( void          *hndl, 
                           OCIError      *err, 
                           OCIDateTime   *datetime, 
                           ub1           *hour,
                           ub1           *min, 
                           ub1           *sec, 
                           ub4           *fsec );
```

## 参数说明

| 参数名        | 说明                                                         |
| ------------- | ------------------------------------------------------------ |
| hndl (IN)     | 用户会话句柄或环境句柄。                                       |
| err (IN/OUT)  | 错误句柄。<br />如果出现错误，则记录在err中，此函数返回OCI_error。<br />通过调用[OCIErrorGet](../辅助功能函数/OCIErrorGet)获取诊断信息。 |
| datetime (IN) | 指向OCIDateTime描述符的指针，从该描述符中检索时间信息。        |
| hour (OUT)    | 检索到的小时值。                                               |
| min (OUT)     | 检索到的分钟值。                                               |
| sec (OUT)     | 检索到的秒值。                                                 |
| fsec (OUT)    | 检索到的小数秒值。                                             |


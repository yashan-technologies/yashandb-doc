## 功能简介

OCIIntervalFromText函数用于解析时间间隔字符串，将给定间隔字符串解析为该字符串所表示的间隔，返回值result的类型即为该间隔的类型。

## 函数声明

```c
sword OCIIntervalFromText ( void           *hndl, 
                            OCIError       *err, 
                            const OraText  *inpstring,
                            size_t          str_len,
                            OCIInterval    *result );
```

## 参数说明

| 参数名         | 说明                                                         |
| -------------- | ------------------------------------------------------------ |
| hndl (IN)      | 用户会话句柄或环境句柄。                                       |
| err (IN/OUT)   | 错误句柄。<br />如果出现错误，则记录在err中，此函数返回OCI_error。<br />通过调用[OCIErrorGet](../辅助功能函数/OCIErrorGet)获取诊断信息。 |
| inpstring (IN) | 输入字符串。                                                   |
| str_len (IN)   | 输入字符串的长度。                                             |
| result (OUT)   | 结果间隔。                                                     |


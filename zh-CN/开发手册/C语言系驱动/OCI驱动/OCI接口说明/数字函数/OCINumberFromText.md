## 功能简介

OCINumberFromText函数用于将字符串换为OCI NUMBER类型。

## 函数声明

```c
sword OCINumberFromText ( OCIError           *err, 
                          const OraText      *str, 
                          ub4                str_length,
                          const OraText      *fmt,
                          ub4                fmt_length, 
                          const OraText      *nls_params, 
                          ub4                nls_p_length, 
                          OCINumber          *number );
```

## 参数说明

|  参数名| 说明|
|-----------------|---------------------------------------------------------------------------------------------------|
| err (IN/OUT)    | 错误句柄。<br />如果出现错误，则记录在err中，此函数返回OCI_error。<br />可以通过调用[OCIErrorGet](../辅助功能函数/OCIErrorGet)获取诊断信息。 |
| str (IN)        | 要转换字符串的指针。                                                                                        |
| str_length (IN) | 字符串的长度。                                                                                           |
| fmt (IN)        | 转换格式。                                                                                             |
| fmt_length (IN) | 转换格式的长度。                                                                                            |
| nls_params (IN) | 全球化支持格式规范。如果它是NULL字符串("")，则使用会话的默认参数。                                                                                        |
| nls_p_length (IN) | 全球化支持格式规范的长度。                                                                                            |
| number  (OUT)   | 将给定的字符串转换为OCI NUMBER类型。                                                                           |

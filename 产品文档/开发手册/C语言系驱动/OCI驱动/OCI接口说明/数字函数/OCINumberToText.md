## 功能简介

OCINumberToText函数用于将OCI NUMBER转换为字符串类型。

## 函数声明

```c
sword OCINumberToText ( OCIError             *err,
                        const OCINumber      *number, 
                        const OraText        *fmt, 
                        ub4                  fmt_length,
                        const OraText        *nls_params, 
                        ub4                  nls_p_length,
                        ub4                  *buf_size, 
                        OraText              *buf );
```

## 参数说明

| 参数名           | 说明                                                                                                |
| ---------------- |---------------------------------------------------------------------------------------------------|
| err (IN/OUT)     | 错误句柄。<br />如果出现错误，则记录在err中，此函数返回OCI_error。<br />可以通过调用[OCIErrorGet](../辅助功能函数/OCIErrorGet)获取诊断信息。 |
| number (IN)      | 要转换的OCI NUMBER。                                                                                   |
| fmt (IN)        | 转换格式。                                                                                             |
| fmt_length (IN) | 转换格式的长度。                                                                                          |
| nls_params (IN) | 全球化支持格式规范。如果它是 NULL 字符串 ("")，则使用会话的默认参数。                                                          |
| nls_p_length (IN) | 全球化支持格式规范的长度。                                                                                     |
| buf_size (IN)        | 缓冲区大小。                                                                                            |
| buf (OUT) | 转换字符串的缓冲区。                                                                                        |

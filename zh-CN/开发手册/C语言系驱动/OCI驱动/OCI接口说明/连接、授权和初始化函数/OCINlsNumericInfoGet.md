## 功能简介

OCINlsNumericInfoGet函数用于获取OCI环境或会话句柄的语言的信息。

## 函数声明

```c
sword OCINlsNumericInfoGet ( void        *hndl, 
                             OCIError    *errhp, 
                             sb4         *val, 
                             ub2          item );
```

## 参数说明

|  参数名| 说明|
| ----------- |----------------------------------------------------------------------------------------------------------------------------------------|
| hndl (IN/OUT)  | 服务上下文的句柄。                                                                                                                               |
| errhp (IN/OUT)  | 一个错误句柄。<br />当出现错误时，可以将其传递给[OCIErrorGet](../辅助功能函数/OCIErrorGet)以获取诊断信息。                                                                                       |
| val (OUT) | 输出数字变量的指针。                                                                                                                             |
| item (IN)  | 指定从OCI环境句柄中获取的项目，可以是以下值之一：<br />OCI_NLS_CHARSET_MAXBYTESZ：OCI环境或会话句柄字符集的最大字符字节大小。<br />OCI_NLS_CHARSET_FIXEDWIDTH：固定宽度字符集的字符字节大小。<br />0：表示可变宽度字符集。 |

部分参数目前仅支持以下取值：

|  参数| 有效值|
| ----- |-------------|
| hndl | envhp句柄     |

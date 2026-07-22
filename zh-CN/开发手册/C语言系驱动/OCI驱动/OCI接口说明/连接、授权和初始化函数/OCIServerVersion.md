## 功能简介

OCIServerVersion函数用于获取Oracle数据库版本，目前固定返回Oracle Database 19c Enterprise Edition Release 19.0.0.0.0 - Production版本号。

## 函数声明

```c
sword OCIServerVersion ( void         *hndlp, 
                         OCIError     *errhp, 
                         OraText      *bufp,
                         ub4           bufsz
                         ub1           hndltype );
```

## 参数说明

|  参数名| 说明|
| -------------- |---------------------------------------------------|
| hndlp (IN/OUT) | 服务上下文的句柄。                                          |
| errhp (IN/OUT) | 一个错误句柄。<br />当出现错误时，可以将其传递给[OCIErrorGet](../辅助功能函数/OCIErrorGet)以获取诊断信息。 |
| bufp (IN/OUT)     | 返回版本信息的缓冲区。                                       |
| bufsz (IN)      | 缓冲区的长度（单位：B）。                                      |
| hndltype (IN)      | 句柄hndlp的句柄类型。                                   |

部分参数目前仅支持以下取值：

|  参数| 有效值|
| ----- | -------------- |
| bufp | Oracle Database 19c Enterprise Edition Release 19.0.0.0.0 - Production |
| hndltype  | OCI_HTYPE_SVCCTX |

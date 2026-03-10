## 功能简介

OCISessionEnd函数用于终止由用户会话上下文。

## 函数声明

```c
sword OCISessionEnd ( OCISvcCtx       *svchp,
                      OCIError        *errhp,
                      OCISession      *usrhp,
                      ub4              mode );
```

## 参数说明

| 参数名         | 说明                                                         |
| -------------- | ------------------------------------------------------------ |
| srvhp (IN/OUT) | 服务上下文的句柄。                                             |
| errhp (IN/OUT) | 一个错误句柄。<br />当出现错误时，可以将其传递给[OCIErrorGet](../辅助功能函数/OCIErrorGet)以获取诊断信息。 |
| usrhp (IN)     | 取消对此用户的身份验证。                                       |
| mode (IN)      | 指定操作模式。                                                 |

部分参数目前仅支持以下取值：

| 参数  | 有效值         |
| ----- | -------------- |
| mode  | OCI_DEFAULT    |
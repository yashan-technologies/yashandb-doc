## 功能简介

OCISessionBegin函数用于创建并开始用户会话。

## 函数声明

```c
sword OCISessionBegin ( OCISvcCtx     *svchp,
                        OCIError      *errhp,
                        OCISession    *usrhp,
                        ub4            credt,
                        ub4            mode );
```

## 参数说明

|  参数名| 说明|
| -------------- | ------------------------------------------------------------ |
| srvhp (IN)     | 服务上下文的句柄。                                             |
| errhp (IN)     | 一个错误句柄。<br />当出现错误时，可以将其传递给[OCIErrorGet](../辅助功能函数/OCIErrorGet)以获取诊断信息。 |
| usrhp (IN/OUT) | 用户会话上下文的句柄，该句柄由此调用初始化。                   |
| credt (IN)     | 指定用于建立用户会话的凭据类型。                               |
| mode (IN)      | 指定操作模式。                                                 |

部分参数目前仅支持以下取值：

|  参数| 有效值|
| ----- | -------------- |
| credt | OCI_CRED_RDBMS |
| mode  | OCI_DEFAULT    |

## 功能简介

OCIHandleFree函数用于显式释放句柄。

## 函数声明

```c
sword OCIHandleFree ( void      *hndlp,
                      ub4        type );
```

## 参数说明

| 参数名     | 说明                           |
| ---------- | ------------------------------ |
| hndlp (IN) | 由[OCIHandleAlloc](OCIHandleAlloc)分配的句柄。 |
| type (IN)  | 指定要释放的存储类型。           |

部分参数目前仅支持以下取值：

| 参数 | 有效值                                                       |
| ---- | ------------------------------------------------------------ |
| type | OCI_HTYPE_ERROR<br />OCI_HTYPE_DESCRIBE<br />OCI_HTYPE_SERVER<br />OCI_HTYPE_SESSION<br />OCI_HTYPE_SVCCTX<br />OCI_HTYPE_STMT<br />OCI_HTYPE_ENV<br />OCI_HTYPE_SPOOL |


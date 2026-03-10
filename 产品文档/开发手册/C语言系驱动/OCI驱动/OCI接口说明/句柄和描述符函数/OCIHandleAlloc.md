## 功能简介

OCIHandleAlloc函数用于返回已分配和初始化句柄。

## 函数声明

```c
sword OCIHandleAlloc ( const void    *parenth,
                       void         **hndlpp, 
                       ub4            type, 
                       size_t         xtramem_sz,
                       void         **usrmempp );
```

## 参数说明

| 参数名          | 说明                                                         |
| --------------- | ------------------------------------------------------------ |
| parenth (IN)    | 环境句柄。                                                     |
| hndlpp (OUT)    | 返回一个句柄。                                                 |
| type (IN)       | 指定要分配的句柄的类型。                                       |
| xtramem_sz (IN) | 指定要分配的用户内存量，保留参数。                         |
| usrmempp (OUT)  | 返回一个指针，保留参数。<br />指向调用为用户分配的大小为xtramem_sz的用户内存。 |

部分参数目前仅支持以下取值：

| 参数 | 有效值                                                                                                                                                      |
| ---- |----------------------------------------------------------------------------------------------------------------------------------------------------------|
| type | OCI_HTYPE_ERROR<br />OCI_HTYPE_DESCRIBE<br />OCI_HTYPE_SERVER<br />OCI_HTYPE_SESSION<br />OCI_HTYPE_SVCCTX<br />OCI_HTYPE_STMT<br />OCI_HTYPE_SPOOL<br/>OCI_HTYPE_TRANS |


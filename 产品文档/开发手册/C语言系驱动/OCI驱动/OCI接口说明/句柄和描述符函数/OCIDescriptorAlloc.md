## 功能简介

OCIDescriptorAlloc函数用于分配存储空间来保存描述符或LOB定位器。

## 函数声明

```c
sword OCIDescriptorAlloc ( const void    *parenth,
                           void         **descpp, 
                           ub4            type,
                           size_t         xtramem_sz,
                           void         **usrmempp);
```

## 参数说明

| 参数名          | 说明                                                         |
| --------------- | ------------------------------------------------------------ |
| parenth (IN)    | 环境句柄。                                                     |
| descpp (OUT)    | 返回所需类型的描述符或LOB定位器。                              |
| type (IN)       | 指定要分配的描述符或LOB定位器的类型。                          |
| xtramem_sz (IN) | 指定在描述符的生存期内要分配给应用程序使用的用户内存量，保留参数。 |
| usrmempp (OUT)  | 返回一个指针，保留参数。<br />指向在描述符的生存期内由调用为用户分配的大小为xtramm_sz的用户内存。 |

部分参数目前仅支持以下取值：

| 参数 | 有效值                                                                                |
| ---- |------------------------------------------------------------------------------------|
| type | OCI_DTYPE_DATE<br/>OCI_DTYPE_TIMESTAMP<br/>OCI_DTYPE_INTERVAL_DS<br/>OCI_DTYPE_LOB |


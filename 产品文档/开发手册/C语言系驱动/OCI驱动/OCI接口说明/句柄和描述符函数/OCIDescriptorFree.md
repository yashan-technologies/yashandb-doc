## 功能简介

OCIDescriptorFree函数用于释放先前分配的描述符。

调用OCIDescriptorFree函数前，必须先调用[OCILobFreeTemporary](../LOB函数/OCILobFreeTemporary)函数。

## 函数声明

```c
sword OCIDescriptorFree ( void     *descp,
                          ub4       type );
```

## 参数说明

| 参数名     | 说明                 |
| ---------- | -------------------- |
| descp (IN) | 已分配的描述符。       |
| type (IN)  | 指定要释放的存储类型。 |

部分参数目前仅支持以下取值：

| 参数 | 有效值                                                       |
| ---- | ------------------------------------------------------------ |
| type | OCI_DTYPE_TIMESTAMP<br/>OCI_DTYPE_INTERVAL_DS<br/>OCI_DTYPE_LOB<br/>OCI_DTYPE_PARAM |
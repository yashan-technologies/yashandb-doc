## 功能简介

OCIAttrSet函数用于设置当前指向OCI句柄的属性。

## 函数声明

```c
sword OCIAttrSet ( void        *trgthndlp,
                   ub4          trghndltyp,
                   void        *attributep,
                   ub4          size,
                   ub4          attrtype,
                   OCIError    *errhp );
```

## 参数说明

| 参数名              | 说明                                          |
| ------------------- | --------------------------------------------- |
| trgthndlp (IN/OUT)  | 其属性被修改的句柄。                     |
| trghndltyp (IN/OUT) | 设置属性的句柄类型。                          |
| attributep (IN)     | 指向属性值的指针。<br />属性值将复制到目标句柄中。 |
| size (IN)           | 属性值的大小。                                |
| attrtype (IN)       | 要设置的属性的类型。                          |
| errhp (IN/OUT)      | 一个错误句柄，当出现错误时可以获取诊断信息。       |


trghndltyp和attrtype参数目前仅支持以下组合值：

| trghndltyp有效值           | attrtype有效值                        |
| ---------------- |------------------------------------|
| OCI_HTYPE_STMT | OCI_ATTR_PREFETCH_ROWS             |
| OCI_HTYPE_SVCCTX | OCI_ATTR_SERVER                    |
| OCI_HTYPE_SVCCTX | OCI_ATTR_SESSION                   |
| OCI_HTYPE_SVCCTX | OCI_ATTR_STMTCACHESIZE（保留参数，暂时不生效） |
| OCI_HTYPE_SESSION | OCI_ATTR_USERNAME                  |
| OCI_HTYPE_SESSION | OCI_ATTR_PASSWORD                  |
| OCI_HTYPE_SESSION | OCI_ATTR_DRIVER_NAME               |
| OCI_HTYPE_SESSION | OCI_ATTR_CONNECTION_CLASS（保留参数，暂时不生效）|
| OCI_HTYPE_SESSION | OCI_ATTR_PURITY（保留参数，暂时不生效） |

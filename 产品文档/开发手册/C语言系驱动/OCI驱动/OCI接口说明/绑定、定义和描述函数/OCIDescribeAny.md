## 功能简介

OCIDescribeAny函数用于描述现有模式和子模式对象。


## 函数声明

```c
sword OCIDescribeAny ( OCISvcCtx       *svchp,
                       OCIError        *errhp,
                       void            *objptr,
                       ub4              objptr_len,
                       ub1              objptr_typ,
                       ub1              info_level,
                       ub1              objtyp,
                       OCIDescribe     *dschp );
```

## 参数说明

| 参数名          | 说明                                                         |
| --------------- | ------------------------------------------------------------ |
| svchp (IN)      | 服务上下文句柄。                                               |
| errhp (IN/OUT)  | 一个错误句柄，当出现错误时可以获取诊断信息。                   |
| objptr (IN)     | 通过为objprt_typ传递不同的值，objptr可能是以下情形之一：<br/>* 包含描述对象名称的字符串。必须是由上一次调用[OCIEnvNlsCreate](../连接、授权和初始化函数/OCIEnvNlsCreate)的charset参数指定的编码。<br/>* 指向TDO的REF的指针（对于类型）。<br/>* 指向TDO的指针（用于类型）。<br/>此参数必须为非NULL。 |
| objnm_len (IN)  | objptr指向的名称字符串的长度。<br />如果传递名称，则必须为非零。<br />如果objptr是指向TDO或其“REF”的指针，则可以为零。 |
| objptr_typ (IN) | objptr中传递的对象的类型。                                   |
| info_level (IN) | 保留以备将来扩展。<br />传递OCI_DEFAULT。                    |
| objtyp (IN)     | 正在描述的架构对象的类型。                                     |
| dschp (IN/OUT)  | 一个描述句柄，该句柄在调用后会填充有关对象的描述信息。<br />必须为非NULL。 |

部分参数目前仅支持以下取值：

| 参数       | 有效值                           |
| ---------- |-------------------------------|
| objptr_typ | OCI_OTYPE_NAME                |
| objtyp     | OCI_PTYPE_VIEW<br />OCI_PTYPE_TABLE |
| objptr     | 非NULL                         |
| objptr_len | 非0                            |
| info_level | 0                             |
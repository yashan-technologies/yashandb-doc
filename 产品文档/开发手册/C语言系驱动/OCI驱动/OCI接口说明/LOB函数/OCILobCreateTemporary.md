## 功能简介

OCILobCreateTemporary函数用于创建临时LOB。

## 函数声明

```c
sword OCILobCreateTemporary(OCISvcCtx          *svchp,
                            OCIError           *errhp,
                            OCILobLocator      *locp,
                            ub2                 csid,
                            ub1                 csfrm,
                            ub1                 lobtype,
                            boolean             cache,
                            OCIDuration         duration);
```

## 参数说明

| 参数名         | 说明                                                                                                                                                          |
| ----------- |-----------------------------------------------------------------------------------------------------|
| svchp (IN) | 服务上下文句柄。                                             |
| errhp (IN/OUT) | 一个错误句柄，当出现错误时可以获取诊断信息。                                               |
| locp (IN/OUT)  | 唯一引用LOB的内部LOB定位器。                                                  |
| csid (IN)  | LOB字符集ID。                                                                                                                                                   |
| csfrm (IN)  | 缓冲区数据的LOB字符集形式，默认值为SQLCS_IMPLICIT。<br />SQLCS_IMPLICIT表示数据库字符集ID，用于创建CLOB。<br />OCI_DEFAULT用于隐式创建CLOB。<br />SQLCS_NCHAR表示NCHAR字符集ID，用于创建NCLOB。 |
| lobtype (IN)  | 要创建的LOB类型。OCI_TEMP_BLOB用于创建临时BLOB，OCI_TEMP_CLOB用于创建临时CLOB或NCLOB。                       |
| cache (IN)  | 如果临时LOB应读入缓存，则传递TRUE；如果不应读入缓存，则传递FALSE。对于NOCACHE功能，默认值为FALSE。                                                       |
| duration (IN)  | 临时LOB的持续时间。有效值为OCI_DURATION_SESSION或OCI_DURATION_CALL。                                                           |

部分参数目前仅支持以下取值：

| 参数   | 有效值                  |
| ------ |----------------------|
| csid | OCI_DEFAULT          |
| csfrm   | SQLCS_IMPLICIT       |
| cache   | FALSE                |
| duration   | OCI_DURATION_SESSION |

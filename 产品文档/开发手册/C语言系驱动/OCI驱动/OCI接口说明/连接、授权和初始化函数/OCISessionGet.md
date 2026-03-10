## 功能简介

OCISessionGet函数用于获取并开始给定服务器的用户会话。

## 函数声明

```c
sword OCISessionGet ( OCIEnv            *envhp,
                      OCIError          *errhp,
                      OCISvcCtx        **svchp,
                      OCIAuthInfo       *authInfop,
                      OraText           *dbName,
                      ub4                dbName_len,
                      const OraText     *tagInfo,
                      ub4                tagInfo_len,
                      OraText          **retTagInfo,
                      ub4               *retTagInfo_len,
                      boolean           *found,
                      ub4                mode );
```

## 参数说明

| 参数名              | 说明                                                         |
| ------------------- | ------------------------------------------------------------ |
| envhp (IN/OUT)      | 环境句柄。                                                     |
| errhp (IN/OUT)      | 一个错误句柄<br />当出现错误时，可以将其传递给[OCIErrorGet](../辅助功能函数/OCIErrorGet)以获取诊断信息。 |
| srvhp (OUT)         | 服务上下文的句柄。                                             |
| authInfop (IN)      | 身份认证句柄。                                                 |
| dbName(IN)          | 指定要使用的数据库服务器。<br />此参数指向指定连接字符串或服务点的字符串。 |
| dbName_len(IN)      | dbname的长度                                                 |
| tagInfo(IN)         | session的类型，保留参数。                                  |
| tagInfo_len(IN)     | session类型的长度，保留参数。                              |
| retTagInfo(OUT)     | 获取到的session类型，保留参数。                            |
| retTagInfo_len(OUT) | 获取到的session类型长度，保留参数。                        |
| found(OUT)          | 是否找到。                                                     |
| mode(IN)            | 模式。                                                         |

部分参数目前仅支持以下取值：

| 参数         | 有效值                                |
|------------|------------------------------------|
| dbName     | 非NULL                              |
| dbName_len | 非0                                 |
| mode       | OCI_DEFAULT<br />OCI_SESSGET_SPOOL |

## 功能简介

OCIStmtPrepare2函数是OCIStmtPrepare函数的扩展版本，用于准备需要执行的SQL或PL语句。与OCIStmtPrepare的主要区别在于支持语句缓存功能和独立的语句句柄管理。

## 函数声明

```c
sword OCIStmtPrepare2 ( OCISvcCtx      *svchp,
                        OCIStmt       **stmthp,
                        OCIError       *errhp,
                        const OraText  *stmttext,
                        ub4             stmt_len,
                        const OraText  *key,
                        ub4             keylen,
                        ub4             language,
                        ub4             mode );
```

## 参数说明

|  参数名| 说明|
| ------------- | ------------------------------------------------------------ |
| svchp (IN)    | 服务上下文句柄。                                             |
| stmthp (OUT)  | 返回语句句柄。                                         |
| errhp (IN)    | 一个错误句柄，当出现错误时可以获取诊断信息。                 |
| stmttext (IN) | 要执行的SQL或PL语句。                                        |
| stmt_len (IN) | 语句的长度，以字符或字节数表示，具体取决于编码。             |
| key (IN)      | 仅用于在语句缓存中搜索语句的键。如果传入键的信息，则语句文本和其他参数将被忽略，仅基于键搜索。 |
| keylen (IN)   | 键的长度。                                                   |
| language (IN) | 语言解析方式。                                               |
| mode (IN)     | 可选择的模式。                                               |

部分参数目前仅支持以下取值：

|  参数| 有效值|
|-----------|----------------|
| stmt     | 非NULL          |
| stmt_len | 非0             |
| key       | NULL           |
| keylen    | 0              |
| language  | OCI_NTV_SYNTAX |
| mode      | OCI_DEFAULT    |

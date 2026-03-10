## 功能简介

OCIStmtRelease函数用于释放语句句柄。

## 函数声明

```c
sword OCIStmtRelease ( OCIStmt        *stmthp,  
                       OCIError       *errhp,
                       const OraText  *key,
                       ub4             keylen,
                       ub4             mode );  
```

## 参数说明

| 参数名     | 说明                                       |
| ---------- | ------------------------------------------ |
| stmthp(IN) | 语句句柄。                                   |
| errhp(IN)  | 一个错误句柄，当出现错误时可以获取诊断信息。 |
| key(IN)    | 语句的键值信息，保留参数。               |
| keylen(IN) | 语句的键值长度，保留参数。               |
| mode(IN)   | 模式，保留参数。                         |

部分参数目前仅支持以下取值：

| 参数   | 有效值                                      |
| ------ |------------------------------------------|
| mode | OCI_STRLS_CACHE_DELETE<br />OCI_DEFAULT  |
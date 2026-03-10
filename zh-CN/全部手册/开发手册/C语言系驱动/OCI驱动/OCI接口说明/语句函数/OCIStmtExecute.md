## 功能简介

OCIStmtExecute函数用于应用程序请求服务器执行SQL或PL语句。

## 函数声明

```c
sword OCIStmtExecute ( OCISvcCtx           *svchp,
                       OCIStmt             *stmtp,
                       OCIError            *errhp,
                       ub4                  iters,
                       ub4                  rowoff,
                       const OCISnapshot   *snap_in,
                       OCISnapshot         *snap_out,
                       ub4                  mode );
```

## 参数说明

|  参数名| 说明|
| -------------- | ------------------------------------------ |
| svchp (IN/OUT) | 服务上下文句柄。                             |
| stmtp (IN/OUT) | 语句句柄。                                   |
| errhp (IN/OUT) | 一个错误句柄，当出现错误时可以获取诊断信息。 |
| iters (IN)     | iters的值。                                  |
| rowoff (IN)    | 数组绑定中的数据与此多行执行相关的起始索引。 |
| snap_in (IN)   | 可选参数。                             |
| snap_out (OUT) | 可选参数。                             |
| mode (IN)      | 可选择的模式。                               |

部分参数目前仅支持以下取值：

|  参数| 有效值|
| -------- | ------------------------------------------------------------ |
| iters    | 行数<br />对于DML语句为paramsetsize<br />对于DQL语句为rowsetsize |
| rowoff   | 0                                                            |
| snap_in  | NULL                                                         |
| snap_out | NULL                                                         |
| mode     | OCI_DEFAULT<br />OCI_DESCRIBE_ONLY<br />OCI_COMMIT_ON_SUCCESS |

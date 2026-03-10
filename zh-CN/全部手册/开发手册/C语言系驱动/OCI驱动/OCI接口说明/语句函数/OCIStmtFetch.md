## 功能简介

OCIStmtFetch函数用于从查询中获取行信息。

## 函数声明

```c
sword OCIStmtFetch ( OCIStmt     *stmtp,
                     OCIError    *errhp, 
                     ub4          nrows,
                     ub2          orientation,
                     ub4          mode );
```

## 参数说明

|  参数名| 说明|
| ---------------- | ------------------------------------------ |
| stmtp (IN)       | 语句句柄。                                   |
| errhp (IN)       | 一个错误句柄，当出现错误时可以获取诊断信息。 |
| nrows (IN)       | 要从当前位置提取的行数。                     |
| orientation (IN) | 默认值为OCI_FETCH_NEXT。                   |
| mode (IN)        | 可选择的模式。                               |

部分参数目前仅支持以下取值：

|  参数| 有效值|
| ----------- | -------------- |
| orientation | OCI_FETCH_NEXT |
| mode        | OCI_DEFAULT    |

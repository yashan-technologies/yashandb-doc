## 功能简介

OCIStmtPrepare函数用于准备需要执行的SQL或PL语句。

## 函数声明

```c
sword OCIStmtPrepare ( OCIStmt       *stmtp,
                       OCIError      *errhp,
                       const OraText *stmt, 
                       ub4            stmt_len,
                       ub4            language,
                       ub4            mode );
```

## 参数说明

|  参数名| 说明|
| ------------- | ------------------------------------------------ |
| stmtp (IN)    | 语句句柄。                                       |
| errhp (IN)    | 一个错误句柄，当出现错误时可以获取诊断信息。     |
| stmt (IN)     | 要执行的SQL或PL语句。                            |
| stmt_len (IN) | 语句的长度，以字符或字节数表示，具体取决于编码。 |
| language (IN) | 语言解析方式。                                   |
| mode (IN)     | 可选择的模式。                                   |

部分参数目前仅支持以下取值：

|  参数| 有效值|
| -------- |----------------|
| stmt     | 非NULL          |
| stmt_len | 非0             |
| language | OCI_NTV_SYNTAX |
| mode     | OCI_DEFAULT    |

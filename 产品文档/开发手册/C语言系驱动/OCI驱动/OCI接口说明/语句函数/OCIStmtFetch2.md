## 功能简介

OCIStmtFetch2函数用于从查询中获取行信息。

## 函数声明

```c
sword OCIStmtFetch2 ( OCIStmt     *stmthp,
                      OCIError    *errhp, 
                      ub4          nrows,
                      ub2          orientation,
                      sb4          fetchOffset,
                      ub4          mode );
```

## 参数说明

| 参数名           | 说明                                                         |
| ---------------- | ------------------------------------------------------------ |
| stmthp (IN)      | 语句句柄。                                                     |
| errhp (IN)       | 一个错误句柄，当出现错误时可以获取诊断信息。                   |
| nrows (IN)       | 要从当前位置提取的行数。                                       |
| orientation (IN) | 默认值为OCI_FETCH_NEXT，设置为OCI_DEFAULT效果与OCI_FETCH_NEXT一样。 |
| fetchOffset (IN) | 与orientation参数一起使用的偏移量，用于改变当前行的位置。只有orientation值为OCI_FETCH_ABSOLUTE或OCI_FETCH_RELATIVE时该参数有效。 |
| mode (IN)        | 可选择的模式。                                                 |

部分参数目前仅支持以下取值：

| 参数        | 有效值                        |
| ----------- |----------------------------|
| orientation | OCI_FETCH_NEXT，OCI_DEFAULT |
| fetchOffset | 0                          |
| mode        | OCI_DEFAULT                |


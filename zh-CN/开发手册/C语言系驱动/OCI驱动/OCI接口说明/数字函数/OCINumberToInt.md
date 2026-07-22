## 功能简介

OCINumberToInt函数用于将OCI NUMBER转换为整数类型。

## 函数声明

```c
sword OCINumberToInt ( OCIError              *err,
                       const OCINumber       *number,
                       uword                 rsl_length,
                       uword                 rsl_flag, 
                       void                  *rsl );
```

## 参数说明

|  参数名| 说明|
| ---------------- |---------------------------------------------------------------------------------------------------|
| err (IN/OUT)     | 错误句柄。<br />如果出现错误，则记录在err中，此函数返回OCI_error。<br />可以通过调用[OCIErrorGet](../辅助功能函数/OCIErrorGet)获取诊断信息。 |
| number (IN)        | 要转换的OCI NUMBER。                                                                                   |
| rsl_length (IN) | 整数的长度。                                                                                            |
| rsl_flag (IN) | 指定整数符号的标志，OCI_NUMBER_UNSIGNED表示无符号值，OCI_NUMBER_SIGNED表示有符号值。                                      |
| rsl (OUT)    | 指向结果的指针。                                                                                          |

部分参数目前仅支持以下取值：

|  参数| 有效值|
| ---- |---------------------------------------------------------|
| rsl_flag (IN) | OCI_NUMBER_UNSIGNED表示无符号值<br />OCI_NUMBER_SIGNED表示有符号值  |

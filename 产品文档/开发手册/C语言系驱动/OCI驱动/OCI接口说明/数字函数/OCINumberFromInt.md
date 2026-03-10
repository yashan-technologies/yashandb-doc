## 功能简介

OCINumberFromInt函数用于将整数转换为OCI NUMBER类型。

## 函数声明

```c
sword OCINumberFromInt ( OCIError            *err,
                         const void          *inum, 
                         uword                inum_length,
                         uword                inum_s_flag,
                         OCINumber           *number );
```

## 参数说明

| 参数名           | 说明                                                                          |
| ---------------- |-----------------------------------------------------------------------------|
| err (IN/OUT)     | 错误句柄。<br />如果出现错误，则记录在err中，此函数返回OCI_error。<br />可以通过调用[OCIErrorGet](../辅助功能函数/OCIErrorGet)获取诊断信息。 |
| inum (IN)        | 要转换整数的指针。                                                                    |
| inum_length (IN) | 整数的长度。                                                                       |
| inum_s_flag (IN) | 指定整数符号的标志，OCI_NUMBER_UNSIGNED表示无符号值，OCI_NUMBER_SIGNED表示有符号值。       |
| number  (OUT)    | 将给定的整数转换为OCI NUMBER类型。                                                        |

部分参数目前仅支持以下取值：

| 参数 | 有效值                                                     |
| ---- |---------------------------------------------------------|
| inum_s_flag | OCI_NUMBER_UNSIGNED表示无符号值<br />OCI_NUMBER_SIGNED表示有符号值  |

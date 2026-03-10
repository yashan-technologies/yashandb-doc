## 功能简介

OCINumberFromInt函数用于将实数（浮点）类型转换为OCI NUMBER类型。

## 函数声明

```c
sword OCINumberFromReal ( OCIError           *err,
                          const void         *rnum,
                          uword              rnum_length,
                          OCINumber          *number );
```

## 参数说明

|  参数名| 说明|
| ---------------- |---------------------------------------------------------------------------------------------------|
| err (IN/OUT)     | 错误句柄。<br />如果出现错误，则记录在err中，此函数返回OCI_error。<br />可以通过调用[OCIErrorGet](../辅助功能函数/OCIErrorGet)获取诊断信息。 |
| rnum (IN)        | 要转换浮点数的指针。                                                                                        |
| rnum_length (IN) | 浮点数的长度。                                                                                            |
| number  (OUT)    | 将给定的浮点数转换为OCI NUMBER类型。                                                                            |

部分参数目前仅支持以下取值：

|  参数| 有效值|
| ---- |----|
| rnum_length | sizeof({float | double}) |

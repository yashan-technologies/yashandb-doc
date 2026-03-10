## 功能简介

OCINumberToReal函数用于将OCI NUMBER转换为实数（浮点）类型。

## 函数声明

```c
sword OCINumberToReal ( OCIError            *err,
                        const OCINumber     *number,
                        uword               rsl_length,
                        void                *rsl );
```

## 参数说明

| 参数名           | 说明                                                                          |
| ---------------- |-----------------------------------------------------------------------------|
| err (IN/OUT)     | 错误句柄。<br />如果出现错误，则记录在err中，此函数返回OCI_error。<br />可以通过调用[OCIErrorGet](../辅助功能函数/OCIErrorGet)获取诊断信息。 |
| number (IN)        | 要转换的OCI NUMBER。                                                                                   |
| rsl_length (IN) | 实数（浮点）的长度。                                                                                            |
| rsl (OUT)    | 指向结果的指针。                                                                                          |

部分参数目前仅支持以下取值：

| 参数 | 有效值           |
| ---- |---------------|
| rsl_length | sizeof({float | double})  |

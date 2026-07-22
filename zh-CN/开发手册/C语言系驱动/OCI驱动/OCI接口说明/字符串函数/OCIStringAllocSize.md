## 功能简介

OCIStringAllocSize函数用于获取分配的字符串内存大小，单位为字节。

## 函数声明

```c
sword OCIStringAllocSize ( OCIEnv              *env,
                           OCIError            *err, 
                           CONST OCIString     *vs,
                           ub4                 *allocsize ); 
```

## 参数说明

|  参数名| 说明|
| ---------------- |--------------------------------------------------------------------------------------|
| err (IN/OUT)     | 错误句柄。<br />如果出现错误，则记录在err中，此函数返回OCI_error。<br />可以通过调用[OCIErrorGet](../辅助功能函数/OCIErrorGet)获取诊断信息。 |
| vs (IN)        | 要返回其分配的大小的字符串。vs必须是非NULL指针。                                                          |
| allocsize (OUT) | 内存大小。                                                                                |

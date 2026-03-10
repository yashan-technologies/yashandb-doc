## 功能简介

OCIMemoryFree函数用于释放内存。

## 函数声明

```c
sword OCIMemoryFree ( void     *hndl, 
                      OCIError *err, 
                      void     *mem );
```

## 参数说明

|  参数名| 说明|
| ----------- |----------------------|
| hndl(IN)    | 环境句柄。                |
| err(IN)     | 错误句柄，当出现错误时可以获取诊断信息。 |
| mem(IN/OUT) | 内存指针。                |

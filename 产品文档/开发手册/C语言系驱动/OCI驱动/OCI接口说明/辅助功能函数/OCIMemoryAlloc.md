## 功能简介

OCIMemoryAlloc函数用于申请内存。

## 函数声明

```c
sword OCIMemoryAlloc( void         *hndl, 
                      OCIError     *err, 
                      void        **mem, 
                      OCIDuration   dur, 
                      ub4           size, 
                      ub4           flags );
```

## 参数说明

| 参数名    | 说明                   |
| --------- |----------------------|
| hndl(IN)  | 环境句柄。                |
| err(IN)   | 错误句柄，当出现错误时可以获取诊断信息。 |
| mem(OUT)  | 申请到的内存指针。            |
| dur(IN)   | 内存的生命周期，保留参数。        |
| size(IN)  | 申请的内存长度。             |
| flags(IN) | 申请模式，保留参数。           |

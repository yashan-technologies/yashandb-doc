## 功能简介

OCISessionPoolDestroy函数用于销毁会话池。

## 函数声明

```c
sword OCISessionPoolDestroy ( OCISPool     *spoolhp, 
                              OCIError     *errhp,
                              ub4           mode );
```

## 参数说明

|  参数名| 说明|
| --------------- | ------------------------------------------------------------ |
| spoolhp(IN/OUT) | 会话池句柄。                                                   |
| errhp(IN/OUT)   | 一个错误句柄。<br />当出现错误时，可以将其传递给[OCIErrorGet](../辅助功能函数/OCIErrorGet)以获取诊断信息。 |
| mode(IN)        | 模式，保留参数。                                           |

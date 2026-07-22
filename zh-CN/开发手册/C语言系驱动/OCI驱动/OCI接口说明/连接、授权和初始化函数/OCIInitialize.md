## 功能简介

OCIInitialize函数用于初始化环境句柄。

## 函数声明

```c
sword OCIInitialize ( ub4            mode,
                      const void    *ctxp, 
                      const void    *(*malocfp) 
                                     ( void   *ctxp,
                                        size_t size ),
                      const void    *(*ralocfp)
                                     ( void  *ctxp,
                                       void  *memptr,
                                       size_t newsize ),
                      const void    (*mfreefp)
                                     ( void  *ctxp,
                                       void  *memptr ));
```

## 参数说明

|  参数名| 说明|
| --------------- | ---------------------------------------------- |
| mode (IN)       | 指定模式的初始化。                             |
| ctxp (IN)       | 指定内存回调例程的用户定义上下文。             |
| malocfp (IN)    | 指定用户定义的内存分配函数。                   |
| ctxp (IN/OUT)   | 为用户定义的内存分配函数指定上下文指针。       |
| size (IN)       | 指定要由用户定义的内存分配函数分配的内存大小。 |
| ralocfp (IN)    | 指定用户定义的内存重新分配函数。               |
| ctxp (IN/OUT)   | 指定用户定义的内存重新分配函数的上下文指针。   |
| memptr (IN/OUT) | 指向内存块的指针。                             |
| newsize (IN)    | 指定要分配的新内存大小。                       |
| mfreefp (IN)    | 指定用户定义的内存释放函数。                   |
| ctxp (IN/OUT)   | 为用户定义的内存释放函数指定上下文指针。       |
| memptr (IN/OUT) | 指向要释放的内存的指针。                       |

部分参数目前仅支持以下取值：

|  参数| 有效值|
| ------ | ------------------------------------------------------------ |
| mode   | OCI_THREADED（默认值）<br />OCI_DEFAULT<br />OCI_OBJECT<br />OCI_EVENTS |
| memptr | NULL                                                         |

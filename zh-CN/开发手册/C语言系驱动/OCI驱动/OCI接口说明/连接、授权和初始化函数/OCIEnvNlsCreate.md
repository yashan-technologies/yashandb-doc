## 功能简介

OCIEnvNlsCreate函数用于创建并初始化环境句柄。

## 函数声明

```c
sword OCIEnvNlsCreate   ( OCIEnv        **envhpp,
                          ub4             mode,
                          void           *ctxp,
                          void          *(*malocfp)
                                             (void  *ctxp,
                                              size_t size),
                          void          *(*ralocfp)
                                             (void  *ctxp,
                                              void  *memptr,
                                              size_t newsize),
                          void          (*mfreefp)
                                             (void  *ctxp,
                                              void  *memptr))
                          size_t          xtramemsz,
                          void          **usrmempp
                          ub2             charset,
                          ub2             ncharset );
```

## 参数说明

|  参数名| 说明|
| -------------- | ------------------------------------------------------- |
| envhpp(OUT)    | 环境句柄。<br />其编码设置由mode指定，该设置由从envhpp派生的语句句柄继承。 |
| mode (IN)      | 指定模式的初始化。                                        |
| ctxp (IN)      | 指定内存回调例程的用户定义上下文，保留参数。               |
| malocfp (IN)   | 指定用户定义的内存分配函数，保留参数。                     |
| ctxp (IN)      | 为用户定义的内存分配函数指定上下文指针，保留参数。         |
| size (IN)      | 指定要由用户定义的内存分配函数分配的内存大小，保留参数。   |
| ralocfp (IN)   | 指定用户定义的内存重新分配函数，保留参数。                 |
| ctxp (IN)      | 指定用户定义的内存重新分配函数的上下文指针，保留参数。     |
| memptr (IN)    | 指向内存块的指针，保留参数。                               |
| newsize (IN)   | 指定要分配的新内存大小，保留参数。                         |
| mfreefp (IN)   | 指定用户定义的内存释放函数，保留参数。                     |
| ctxp (IN)      | 为用户定义的内存释放函数指定上下文指针，保留参数。         |
| memptr (IN)    | 指向要释放的内存的指针，保留参数。                         |
| xtramemsz (IN) | 指定在环境持续时间内要分配的用户内存量，保留参数。         |
| usrmempp (OUT) | 返回一个指针，保留参数。<br />指向由调用为用户分配的xtramemsz大小的用户内存。 |
| charset(IN)    | 客户端字符集。                                            |
| ncharset(IN)   | 客户端国家字符集，保留参数。                               |

部分参数目前仅支持以下取值：

|  参数| 有效值|
| ------- | ------------------------------------------------ |
| mode    | OCI_THREADED（默认值）<br />OCI_DEFAULT  <br /> OCI_NCHAR_LITERAL_REPLACE_ON <br /> OCI_NCHAR_LITERAL_REPLACE_OFF        |
| memptr  | NULL                                             |
| charset | 0（采用默认值）<br />852（GBK）<br />871（UTF8） |

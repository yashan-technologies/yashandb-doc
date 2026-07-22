## 功能简介

OCILobWrite2函数用于将缓冲区写入LOB。

## 函数声明

```c
sword OCILobWrite2 ( OCISvcCtx       *svchp,
                     OCIError        *errhp,
                     OCILobLocator   *locp,
                     oraub8          *byte_amtp,
                     oraub8          *char_amtp,
                     oraub8           offset,
                     void            *bufp, 
                     oraub8           buflen,
                     ub1              piece,
                     void            *ctxp, 
                     OCICallbackLobWrite2 (cbfp)
                                     (
                                       void     *ctxp,
                                       void     *bufp,
                                       oraub8   *lenp,
                                       ub1      *piecep
                                       void    **changed_bufpp,
                                       oraub8   *changed_lenp
                                     ) 
                     ub2              csid,
                     ub1              csfrm );
```

## 参数说明

|  参数名| 说明|
| -------------- |----------------------------------------------------------------------------------------------------------|
| svchp (IN/OUT) | 服务上下文句柄。                                                                                                 |
| errhp (IN/OUT) | 一个错误句柄，当出现错误时可以获取诊断信息。                                                                                   |
| locp (IN/OUT)  | 唯一引用LOB的内部LOB定位器。                                                                                        |
| byte_amtp (IN/OUT)  | 作为入参，表示写入数据库的字节数，始终用于BLOB，对于CLOB和NCLOB，只有char_amtp为零时才会使用。<br/>作为出参：表示写入数据库的字节数。在轮询模式下，是刚刚写入的片段的长度（单位：字节）。 |
| char_amtp (IN/OUT)  | 作为入参，表示写入数据库的最大字符数，BLOB忽略。<br/>作为出参：表示写入数据库的字符数，BLOB下未定义。在轮询模式下，表示刚刚写入的片段的长度（以字符为单位）。                    |
| offset (IN)    | LOB值开始的绝对偏移量。<br/>如果指向BLOB，表示字节数。<br/>如果指向CLOB或NCLOB，表示字符数。                                              |
| bufp (IN)      | 写入的缓冲区。                                                                                                  |
| buflen (IN)    | 缓冲区中数据的长度（单位：字节）。                                                                                        |
| piece (IN)     | 正在写入的缓冲区部分。                                                                                              |
| ctxp (IN)      | 回调函数的上下文指针。                                                                                              |
| cbfp (IN)      | 一个回调函数，可以注册为在分段写入中为每个片段调用。                                                                               |
| ctxp (IN)      | 回调函数的上下文。                                                                                                |
| bufp (IN/OUT)  | 缓冲区。                                                                                                     |
| lenp (IN/OUT)  | 缓冲区（in）中数据的长度（单位：字节），以及bufp中当前片段的长度（单位：字节）。                                                              |
| piecep (OUT)   | 取值范围：OCI_NEXT_PIECE或OCI_LAST_PIECE。                                                                      |
| changed_bufpp (OUT)      | 如果回调函数希望使用新缓冲区来读取下一个片段，则可以放置新缓冲区的地址。如果此参数设置为NULL，则使用默认的旧缓冲区bufp。                                         |
| changed_lenp (OUT)     | 新缓冲区长度。                                                                                                  |
| csid (IN)      | 缓冲区数据的字符集ID。                                                                                             |
| csfrm (IN)     | 缓冲区数据的字符集形式。                                                                                             |

部分参数目前仅支持以下取值：

|  参数| 有效值|
|---------| -------------- |
| piecep  | OCI_ONE_PIECE  |
| ctxp    | NULL           |
| cbfp    | NULL           |
| csid    | 0<br/>与当前客户端环境一致的字符集ID|
| csfrm   | SQLCS_IMPLICIT<br/>SQLCS_NCHAR |

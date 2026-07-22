## 功能简介

OCILobRead2函数是OCILobRead函数的扩展版本，用于将LOB读入缓冲区。与OCILobRead的主要区别在于使用oraub8类型替代ub4类型，支持超过4GB的LOB读取操作。

## 函数声明

```c
sword OCILobRead2 ( OCISvcCtx           *svchp,
                    OCIError            *errhp,
                    OCILobLocator       *locp,
                    oraub8              *byte_amtp,
                    oraub8              *char_amtp,
                    oraub8               offset,
                    void                *bufp,
                    oraub8               bufl,
                    ub1                  piece,
                    void                *ctxp, 
                    OCICallbackLobRead2 (cbfp)
                                        ( void          *ctxp,
                                          const void    *bufp,
                                          oraub8         lenp,
                                          ub1            piecep
                                          void         **changed_bufpp,
                                          oraub8        *changed_lenp
                                        )
                    ub2                  csid,
                    ub1                  csfrm );
```

## 参数说明

|  参数名| 说明|
| -------------- |---------------------------------------------------------------------------------------------------------------------|
| svchp (IN/OUT) | 服务上下文句柄。                                                                                                            |
| errhp (IN/OUT) | 一个错误句柄，当出现错误时可以获取诊断信息。                                                                                              |
| locp (IN)      | 唯一引用LOB的LOB定位器。                                                                                                     |
| byte_amtp (IN/OUT) | 作为入参，表示从数据库读取的字节数，始终用于BLOB，对于CLOB和NCLOB，只有char_amtp为零时才会使用。<br/>作为出参：表示读入用户缓冲区的字节数。                                 |
| char_amtp (IN/OUT) | 作为入参，表示要读入用户缓冲区的最大字符数。<br/>作为出参：表示读入用户缓冲区的字符数。<br /> 指向BLOB时，该参数始终不生效。                                              |
| offset (IN)    | LOB值开始的绝对偏移量。<br/>如果指向BLOB，表示字节数。<br/>如果指向CLOB或NCLOB，表示字符数。                                                         |
| bufp (IN/OUT)  | 数据缓冲区，分配的内存的长度假定为bufl。                                                                                              |
| bufl (IN)      | 缓冲区的长度。                                                                                                             |
| piece (IN)     | 不进行轮询，调用传递OCI_ONE_PIECE。<br/>要进行轮询，请在第一次调用时传递OCI_FIRST_PIECE，并在随后的调用中传递OCI_NEXT_PIECE。<br/>使用回调时应传递OCI_FIRST_PIECE。 |
| ctxp (IN)      | 回调函数的上下文指针。                                                                                                         |
| cbfp (IN)      | 回调函数。                                                                                                               |
| lenp (IN)      | 缓冲区中当前片段的长度（单位：B）。                                                                                                  |
| piecep (IN)    | 取值范围：OCI_FIRST_PIECE、OCI_NEXT_PIECE或OCI_LAST_PIECE。                                                                 |
| changed_bufpp (OUT) | 设置为输入新缓冲区的地址时，表示回调函数使用新缓冲区来读取下一块数据。<br/>设置为NULL时，表示使用默认的旧缓冲区bufp。                                                   |
| changed_lenp (OUT)  | 新缓冲区的长度。                                                                                                            |
| csid (IN)      | 缓冲区数据的字符集ID。                                                                                                        |
| csfrm (IN)     | 缓冲区数据的字符集形式。<br/>非0值取值范围：SQLCS_IMPLICIT表示数据库字符集ID，SQLCS_NCHAR表示NCHAR字符集ID。<br/>如果未指定csfrm，则使用默认值SQLCS_IMPLICIT。     |

部分参数目前仅支持以下取值：

|  参数| 有效值|
| ----- |---------------------|
| offset| 最小值1，最大值lobLength+1 |
| piece | OCI_ONE_PIECE<br/>OCI_FIRST_PIECE |
| csid  | 0<br/>与当前客户端环境一致的字符集ID。 |
| csfrm | SQLCS_IMPLICIT<br/>SQLCS_NCHAR |

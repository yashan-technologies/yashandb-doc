## 功能简介

OCILobRead函数用于将LOB读入缓冲区。

## 函数声明

```c
sword OCILobRead ( OCISvcCtx          *svchp,
                   OCIError           *errhp,
                   OCILobLocator      *locp,
                   ub4                *amtp,
                   ub4                 offset,
                   void               *bufp,
                   ub4                 bufl,
                   void               *ctxp, 
                   OCICallbackLobRead (cbfp)
                                      ( void          *ctxp,
                                        const void    *bufp,
                                        ub4            len,
                                        ub1            piece 
                                      )
                   ub2                 csid,
                   ub1                 csfrm );
```

## 参数说明

|  参数名| 说明|
| -------------- |-------------------------------------------------------------|
| svchp (IN/OUT) | 服务上下文句柄。                                                    |
| errhp (IN/OUT) | 一个错误句柄，当出现错误时可以获取诊断信息。                                      |
| locp (IN)      | 唯一引用LOB的LOB定位器。                                             |
| amtp (IN/OUT)  | 以字节或字符为单位的数量。                                               |
| offset (IN)    | LOB值开始的绝对偏移量。<br/>如果指向BLOB，表示字节数。<br/>如果指向CLOB或NCLOB，表示字符数。 |
| bufp (IN/OUT)  | 数据缓冲区，所分配的内存的长度假定为bufl。                                     |
| bufl (IN)      | 缓冲区的长度，以字节数表示。                                              |
| ctxp (IN)      | 回调函数的上下文指针。                                                 |
| cbfp (IN)      | 回调函数。                                                       |
| ctxp (IN)      | 回调函数的上下文。                                                   |
| bufp (IN/OUT)  | 数据缓冲区。                                                      |
| len (IN)       | 缓冲区中当前片段的长度（单位：B）。                                          |
| piece (IN)     | 取值范围：OCI_FIRST_PIECE、OCI_NEXT_PIECE或OCI_LAST_PIECE。         |
| csid (IN)      | 缓冲区数据的字符集ID。                                                |
| csfrm (IN)     | 缓冲区数据的字符集形式。                                                |

部分参数目前仅支持以下取值：

|  参数| 有效值|
| ----- |---------------------|
| offset| 最小值1，最大值lobLength+1 |
| ctxp  | NULL                |
| cbfp  | NULL                |
| csid  | 0                   |
| csfrm | SQLCS_IMPLICIT      |

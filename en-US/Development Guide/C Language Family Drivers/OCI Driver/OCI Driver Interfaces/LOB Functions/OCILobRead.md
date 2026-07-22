## Functionality Introduction

The OCILobRead function is used to read a LOB into the cache.

## Function Declaration

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

## Parameter Description

|Parameter Name |Description |
| -------------- |-------------------------------------------------------------|
| svchp (IN/OUT)   | Service context handle.                                     |
| errhp (IN/OUT)   | An error handle to obtain diagnostic information when an error occurs. |
| locp (IN)        | A LOB locator that uniquely references the LOB.            |
| amtp (IN/OUT)    | The amount in bytes or characters.                         |
| offset (IN)      | The absolute offset where the LOB value starts.<br/> If targeting a BLOB, it refers to byte count.<br/> If targeting a CLOB or NCLOB, it refers to character count. |
| bufp (IN/OUT)    | Data cache, with the allocated memory length assumed to be bufl. |
| bufl (IN)        | Length of the cache expressed in bytes.                    |
| ctxp (IN)        | Context pointer for the callback function.                 |
| cbfp (IN)        | Callback function.                                         |
| ctxp (IN)        | Context for the callback function.                         |
| bufp (IN/OUT)    | Data cache.                                              |
| len (IN)         | Length of the current piece in the cache (in bytes).       |
| piece (IN)       | Value range: OCI_FIRST_PIECE, OCI_NEXT_PIECE, or OCI_LAST_PIECE. |
| csid (IN)        | Character set ID of the cache data.                        |
| csfrm (IN)       | Character set form of the cache data.                     |

Some parameters currently only support the following values:

|Parameter |Valid Values |
| ----- |---------------------|
| offset    | Minimum 1, Maximum lobLength + 1 |
| ctxp  | NULL                |
| cbfp  | NULL                |
| csid  | 0                   |
| csfrm | SQLCS_IMPLICIT      |

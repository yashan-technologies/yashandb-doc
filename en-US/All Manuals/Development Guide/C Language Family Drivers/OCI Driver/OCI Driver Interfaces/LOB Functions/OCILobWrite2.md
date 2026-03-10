## Functionality Overview

The OCILobWrite2 function is used to write cache to a LOB.

## Function Declaration

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

## Parameter Description

|Parameter Name |Description |
| -------------- |----------------------------------------------------------------------------------------------------------|
| svchp (IN/OUT)    | Service context handle.                                                                                   |
| errhp (IN/OUT)    | An error handle that can be used to get diagnostic information when an error occurs.                     |
| locp (IN/OUT)     | An internal LOB locator that uniquely references the LOB.                                                 |
| byte_amtp (IN/OUT)| As input: represents the number of bytes to be written to the database, always used for BLOB. For CLOB and NCLOB, it is used only when char_amtp is zero.<br/>As output: represents the number of bytes written to the database. In polling mode, it is the length of the segment just written (in bytes). |
| char_amtp (IN/OUT)| As input: represents the maximum number of characters to be written to the database, ignored for BLOB.<br/>As output: represents the number of characters written to the database, undefined for BLOB. In polling mode, it represents the length of the segment just written (in characters). |
| offset (IN)       | The absolute offset where the LOB value starts.<br/>If it points to a BLOB, it represents byte count.<br/>If it points to CLOB or NCLOB, it represents character count. |
| bufp (IN)         | The cache to be written.                                                                                  |
| buflen (IN)       | The length of data in the cache (in bytes).                                                               |
| piece (IN)        | The part of the cache being written.                                                                       |
| ctxp (IN)         | A pointer to the context for the callback function.                                                        |
| cbfp (IN)         | A callback function that can be registered to be called for each piece during segmented writing.           |
| ctxp (IN)         | The context for the callback function.                                                                     |
| bufp (IN/OUT)     | The cache.                                                                                                |
| lenp (IN/OUT)     | The length of the data in the cache (in) (in bytes), and the length of the current piece in bufp (in bytes). |
| piecep (OUT)      | Possible values: OCI_NEXT_PIECE or OCI_LAST_PIECE.                                                      |
| changed_bufpp (OUT)| If the callback function wants to use a new cache to read the next piece, the address of the new cache can be placed here. If this parameter is set to NULL, the default old cache bufp is used. |
| changed_lenp (OUT)| Length of the new cache.                                                                                   |
| csid (IN)         | Character set ID of the cache data.                                                                        |
| csfrm (IN)        | Character set form of the cache data.                                                                      |

Currently, some parameters only support the following values:

|Parameter |Valid Values |
|---------| -------------- |
| piecep  | OCI_ONE_PIECE  |
| ctxp    | NULL           |
| cbfp    | NULL           |
| csid      | 0<br/>Character set ID consistent with the current client environment |
| csfrm   | SQLCS_IMPLICIT<br/>SQLCS_NCHAR |

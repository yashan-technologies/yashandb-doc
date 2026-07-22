## Functionality Introduction

The OCILobRead2 function is used to read LOB into cache.

## Function Declaration

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

## Parameter Description

|Parameter Name |Description |
| -------------- |---------------------------------------------------------------------------------------------------------------------|
| svchp (IN/OUT)    | Service context handle.                                                                                           |
| errhp (IN/OUT)    | An error handle that can provide diagnostic information when an error occurs.                                     |
| locp (IN)         | A LOB locator that uniquely references the LOB.                                                                  |
| byte_amtp (IN/OUT)| As an input parameter, it indicates the number of bytes to read from the database, always used for BLOB; for CLOB and NCLOB, it is only used when char_amtp is zero.<br/>As an output parameter: it indicates the number of bytes read into user cache. |
| char_amtp (IN/OUT)| As an input parameter, it indicates the maximum number of characters to read into user cache.<br/>As an output parameter: it indicates the number of characters read into user cache.<br/>This parameter is always ineffective when pointing to BLOB. |
| offset (IN)       | The absolute offset where the LOB value starts.<br/>If pointing to BLOB, it represents a number of bytes.<br/>If pointing to CLOB or NCLOB, it represents a number of characters. |
| bufp (IN/OUT)     | Data cache, with the length of allocated memory assumed to be bufl.                                              |
| bufl (IN)         | Length of the cache.                                                                                              |
| piece (IN)        | For no polling, pass OCI_ONE_PIECE.<br/>To poll, pass OCI_FIRST_PIECE on the first call and OCI_NEXT_PIECE on subsequent calls.<br/>When using a callback, OCI_FIRST_PIECE should be passed. |
| ctxp (IN)         | Context pointer for the callback function.                                                                        |
| cbfp (IN)         | Callback function.                                                                                                |
| lenp (IN)        | Length of the current segment in the cache (units: B).                                                          |
| piecep (IN)       | Possible values: OCI_FIRST_PIECE, OCI_NEXT_PIECE, or OCI_LAST_PIECE.                                          |
| changed_bufpp (OUT) | When set to the address of new cache input, it indicates that the callback function uses the new cache to read the next piece of data.<br/>When set to NULL, it indicates using the default old cache bufp. |
| changed_lenp (OUT)   | Length of the new cache.                                                                                        |
| csid (IN)        | Character set ID of the cache data.                                                                               |
| csfrm (IN)       | Character set form of the cache data.<br/>Non-zero values range: SQLCS_IMPLICIT represents the database character set ID, SQLCS_NCHAR represents the NCHAR character set ID.<br/>If csfrm is not specified, the default value SQLCS_IMPLICIT is used. |

Some parameters currently only support the following values:

|Parameter |Valid Values |
| ----- |---------------------|
| offset    | Minimum value 1, maximum value lobLength + 1 |
| piece | OCI_ONE_PIECE<br/>OCI_FIRST_PIECE |
| csid      | 0<br/>Character set ID that is consistent with the current client environment. |
| csfrm | SQLCS_IMPLICIT<br/>SQLCS_NCHAR |

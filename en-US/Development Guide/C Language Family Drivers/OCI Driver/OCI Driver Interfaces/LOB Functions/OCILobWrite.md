## Functionality Overview

The OCILobWrite function is used to write a cache to a LOB.

## Function Declaration

```c
sword OCILobWrite ( OCISvcCtx       *svchp,
                    OCIError        *errhp,
                    OCILobLocator   *locp,
                    ub4             *amtp,
                    ub4              offset,
                    void            *bufp, 
                    ub4              buflen,
                    ub1              piece,
                    void            *ctxp, 
                    OCICallbackLobWrite      (cbfp)
                                    (
                                      void     *ctxp,
                                      void     *bufp,
                                      ub4      *lenp,
                                      ub1      *piecep
                                    ) 
                    ub2              csid,
                    ub1              csfrm );
```

## Parameter Description

|Parameter Name |Description |
| -------------- |-------------------------------------------------------------|
| svchp (IN/OUT)  | Service context handle.                                       |
| errhp (IN/OUT)  | An error handle to obtain diagnostic information when an error occurs. |
| locp (IN/OUT)   | Internal LOB locator that uniquely references the LOB.       |
| amtp (IN/OUT)   | Amount in bytes or characters.                               |
| offset (IN)     | Absolute offset where the LOB value starts.<br/>For BLOB, it represents bytes.<br/>For CLOB or NCLOB, it represents characters. |
| bufp (IN)       | The cache to be written.                                     |
| buflen (IN)     | Length of data in the cache (in bytes).                     |
| piece (IN)      | The portion of the cache being written.                      |
| ctxp (IN)       | Context pointer for the callback function.                   |
| cbfp (IN)       | A callback function that can be registered to be called for each piece during segmented writes. |
| ctxp (IN)       | Context for the callback function.                           |
| bufp (IN/OUT)   | Cache.                                                      |
| lenp (IN/OUT)   | Length of data in the cache (in) in bytes, and length of the current segment in bufp (in bytes). |
| piecep (OUT)    | Valid values: OCI_NEXT_PIECE or OCI_LAST_PIECE.            |
| csid (IN)       | Character set ID of the cache data.                         |
| csfrm (IN)      | Character set form of the cache data.                       |

Some parameters currently only support the following values:

|Parameter |Valid Values |
|---------| -------------- |
| piecep  | OCI_ONE_PIECE  |
| ctxp    | NULL           |
| cbfp    | NULL           |
| csid       | 0<br/>Character set ID consistent with the current client environment. |
| csfrm   | SQLCS_IMPLICIT<br/>SQLCS_NCHAR |

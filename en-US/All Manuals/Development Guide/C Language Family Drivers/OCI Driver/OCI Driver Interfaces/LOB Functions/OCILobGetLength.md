## Functionality Introduction

The OCILobGetLength function is used to obtain the length of a LOB.

## Function Declaration

```c
sword OCILobGetLength ( OCISvcCtx      *svchp,
                        OCIError       *errhp,
                        OCILobLocator  *locp,
                        ub4            *lenp );
```

## Parameter Description

|Parameter Name |Description |
| -------------- | ------------------------------------------------------------ |
| svchp (IN)       | Service context handle.                                    |
| errhp (IN/OUT)   | An error handle to obtain diagnostic information in case of an error. |
| locp (IN)        | A locator that uniquely identifies the LOB.               |
| lenp (OUT)       | If the LOB is not NULL, lenp returns the length of the LOB:<br/>If it points to a BLOB, lenp returns the byte length.<br/>If it points to a CLOB or NCLOB, lenp returns the character length. |
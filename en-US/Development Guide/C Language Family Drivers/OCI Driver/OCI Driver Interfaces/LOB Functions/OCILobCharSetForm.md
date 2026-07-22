## Functionality Introduction

The OCILobCharSetForm function is used to retrieve the character set form of the LOB.

## Function Declaration

```c
sword OCILobCharSetForm ( OCIEnv                *envhp, 
                          OCIError              *errhp, 
                          const OCILobLocator   *locp, 
                          ub1                   *csfrm );
```

## Parameter Description

|Parameter Name |Description |
| ------------ |---------------------------------------------------------------------------------------------------------------------------------------------------|
| envhp (IN/OUT) | Environment handle.                                                                                                                                           |
| errhp (IN/OUT) | An error handle that allows for diagnosis information when an error occurs.                                                                                      |
| locp (IN)      | The internal LOB locator that uniquely refers to the LOB.                                                                                     |
| csfrm (OUT)    | Outputs the character set form of the LOB, cannot be NULL. Returns SQLCS_IMPLICIT to indicate the database character set ID, defaulting to this value; returns SQLCS_NCHAR to indicate NCHAR character set ID.<br />If the input LOB type is BLOB, since binary BLOBs do not have the concept of character set, csfrm returns 0. |

Some parameters currently only support the following values:

|Return Parameter |Valid Values |
|-------|-----------------------------|
| csfrm | SQLCS_IMPLICIT<br/>SQLCS_NCHAR<br/>0 |

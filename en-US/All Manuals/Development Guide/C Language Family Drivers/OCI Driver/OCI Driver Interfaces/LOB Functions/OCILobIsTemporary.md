## Functionality Introduction

The OCILobIsTemporary function is used to determine whether a LOB is a temporary LOB.

## Function Declaration

```c
sword OCILobIsTemporary(OCIEnv            *envhp,
                        OCIError          *errhp,
                        OCILobLocator     *locp,
                        boolean           *is_temporary);
```

## Parameter Description

|Parameter Name |Description |
| ----------- |---------------------------------------------|
| envhp (IN)      | Environment handle.                          |
| errhp (IN/OUT)  | An error handle that provides diagnostic information when an error occurs. |
| locp (IN)       | The internal LOB locator that uniquely refers to the LOB. |
| is_temporary (OUT) | Returns TRUE if it is a temporary LOB, otherwise returns FALSE. |
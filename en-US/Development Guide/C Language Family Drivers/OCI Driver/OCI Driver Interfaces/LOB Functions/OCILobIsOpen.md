## Functionality Introduction

The OCILobIsOpen function is used to determine whether a LOB is open.

## Function Declaration

```c
sword OCILobIsOpen ( OCISvcCtx        *svchp,
                     OCIError         *errhp, 
                     OCILobLocator    *locp, 
                     boolean          *flag );
```

## Parameter Description

|Parameter Name |Description |
| ------ |------------------------|
| svchp (IN/OUT) | Service context handle.               |
| errhp (IN/OUT) | An error handle that can be used to obtain diagnostic information when an error occurs. |
| locp (IN)      | The LOB locator that uniquely references the LOB. |
| flag (OUT)     | Result indicating whether it is open. |
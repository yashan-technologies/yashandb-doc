## Functionality Introduction

The OCILobOpen function is used to open a LOB in a specified mode.

## Function Declaration

```c
sword OCILobOpen ( OCISvcCtx        *svchp,
                   OCIError         *errhp, 
                   OCILobLocator    *locp, 
                   ub1               mode );
```

## Parameter Description

|Parameter Name |Description |
| -------------- | -------------------------------------------- |
| svchp (IN)      | Service context handle.                            |
| errhp (IN/OUT)  | An error handle to retrieve diagnostic information when an error occurs. |
| locp (IN)       | The LOB to be opened. The locator can refer to internal or external LOBs.   |
| mode (IN)       | The mode in which to open the LOB.                |
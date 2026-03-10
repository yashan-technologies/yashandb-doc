## Functionality Overview

The OCILobClose function is used to close a previously opened LOB.

## Function Declaration

```c
sword OCILobClose ( OCISvcCtx      *svchp,
                    OCIError       *errhp, 
                    OCILobLocator  *locp );
```

## Parameter Description

|Parameter Name |Description |
| -------------- | -------------------------------------------- |
| svchp (IN)       | The service context handle.                   |
| errhp (IN/OUT)   | An error handle that can be used to obtain diagnostic information when an error occurs. |
| locp (IN/OUT)    | The LOB to be closed. The locator can reference internal or external LOBs.   |
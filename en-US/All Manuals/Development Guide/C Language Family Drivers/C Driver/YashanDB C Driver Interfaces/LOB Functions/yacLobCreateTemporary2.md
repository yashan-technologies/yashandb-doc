## Functionality Overview

The yacLobCreateTemporary2 function is used to notify the server to create a temporary LOB in the LOB protocol. It returns YAC_SUCCESS to indicate successful creation and YAC_ERROR to indicate creation failure.

## Function Declaration

```c
YacResult yacLobCreateTemporary2(YacHandle hConn,
                                 YacLobLocator* loc,
                                 YacTempLobType tempLobType)
```

## Parameter Description

|Parameter Name |Description |
| ---------------- | -------------------------------- |
| hConn (IN/OUT)      | A handle that records various information related to the connection. |
| loc (IN)            | The loblocator in the LOB protocol.  |
| tempLobType (IN)    | The type of the temporary LOB to be created. |
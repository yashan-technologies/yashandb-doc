## Functionality Introduction

The yacLobFreeTemporary function is used to notify the server to release the temporary LOB in the LOB protocol. A return value of YAC_SUCCESS indicates that the release was successful, while a return value of YAC_ERROR indicates that the release failed.

## Function Declaration

```c
yacResult yacLobFreeTemporary ( yacHandle      hConn,
                                yacLobLocator* locator);
```

## Parameter Description

|Parameter Name |Description |
| -------------- | -------------------------------- |
| hConn (IN/OUT)  | A handle that records various information related to the connection. |
| locator (IN)    | The loblocator in the LOB protocol. |
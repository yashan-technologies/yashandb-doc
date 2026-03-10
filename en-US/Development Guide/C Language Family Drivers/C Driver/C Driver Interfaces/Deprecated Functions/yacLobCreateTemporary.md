## Functionality Introduction

The yacLobCreateTemporary function is used in the LOB protocol to notify the server to create a temporary LOB. A return value of YAC_SUCCESS indicates that the creation was successful, while a return value of YAC_ERROR indicates that the creation failed.

> **Note**: 
>
> This function is deprecated. It is recommended to use [yacLobCreateTemporary2](../LOB Functions/yacLobCreateTemporary2).

## Function Declaration

```c
yacResult yacLobCreateTemporary ( yacHandle      hConn,
                                  yacLobLocator* locator);
```

## Parameter Description

|Parameter Name |Description |
| -------------- | -------------------------------- |
| hConn (IN/OUT)   | A handle that records various information related to the connection. |
| locator (IN)     | The loblocator in the LOB protocol.   |
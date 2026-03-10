## Functionality Overview

The `yacSetConnAttr` function is used to set a specific property value in the connection information handle. Returning `YAC_SUCCESS` indicates that the setting was successful, while returning `YAC_ERROR` indicates that the setting failed.

Parameters at the connection level will affect the behavior of all handles under the current connection information handle. Please configure and retrieve them using `yacSetConnAttr()` and the [yacGetConnAttr](yacGetConnAttr) function.

|Property |Value Data Type |Read/Write Privilege (R/W) |Description |
|-----------------------------|----------------------|-------------|------------------------------------------------------|
| YAC_ATTR_LOGIN_TIMEOUT         | YacUint32             | R/W                         | This value specifies the timeout for the current login process, reserved parameter. |
| YAC_ATTR_AUTOCOMMIT            | YacBool               | R/W                         | This value specifies whether to automatically commit at the end of a statement. |
| YAC_ATTR_PACKET_SIZE           | YacUint32             | R/W                         | This value specifies the packet size during session interaction. |
| YAC_ATTR_TXN_ISOLATION         | YacTxnIsolation       | R/W                         | This value specifies the isolation level of the transaction executed in the session.<br />Setting this attribute requires that the current session is not in a transaction or must be the first statement of a transaction. |
| YAC_ATTR_CREDT                 | YacCredtType          | R/W                         | This value specifies the authentication type of the session. |
| YAC_ATTR_TAF_CALLBACK          | YacTafCallbackStruct  | R/W                         | This value is used to set the TAF callback function.      |
| YAC_ATTR_HEARTBEAT_ENABLED     | YacBool               | R/W                         | This value is used to set or query whether to use the heartbeat detection functionality. |
| YAC_ATTR_TAF_ENABLED           | YacBool               | R                           | This value returns whether TAF functionality is enabled.  |
| YAC_ATTR_MAX_CHARSET_RATIO     | YacUint32             | R                           | This value returns the maximum CHARSET expansion ratio.   |
| YAC_ATTR_MAX_NCHARSET_RATIO    | YacUint32             | R                           | This value returns the maximum NCHARSET expansion ratio.  |

## Function Declaration

```c
YacResult yacSetConnAttr(yacHandle hConn,
                         yacConnAttr attr,
                         YacVoid* value,
                         YacInt32 bufLength);
```

## Parameter Description

|Parameter Name |Description |
| -------------- | ---------------------------- |
| hConn (IN/OUT) | Connection information handle.   |
| attr (IN)      | Attribute type.                 |
| value (IN)     | The attribute value to be set.   |
| bufLength (IN) | The cache length of the attribute value to be set. |
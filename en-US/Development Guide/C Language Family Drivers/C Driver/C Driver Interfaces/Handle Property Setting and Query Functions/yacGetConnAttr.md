## Functionality Introduction

The yacGetConnAttr function is used to retrieve a specific attribute value from a connection information handle. It returns YAC_SUCCESS to indicate success and YAC_ERROR to indicate failure.

Parameters at the conn level will affect the behavior of all handles under the current connection information handle. Please configure relevant settings using the [yacSetConnAttr](yacSetConnAttr) function.

## Function Declaration

```c
yacResult yacGetConnAttr(yacHandle hConn,
                         yacConnAttr attr,
                         YacVoid* value,
                         YacInt32 bufLength,
                         YacInt32* stringLength);
```

## Parameter Description

|Parameter Name |Description |
| ------------------ | ---------------------------- |
| hEnv (IN/OUT)      | Environment handle.           |
| attr (IN)          | Attribute type.               |
| value (OUT)        | Stores the retrieved attribute value. |
| bufLength (OUT)    | Cache length of the retrieved attribute value. |
| stringLength (OUT) | Actual length of the retrieved attribute value. |
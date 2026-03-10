## Functionality Introduction

The yacGetEnvAttr function is used to retrieve a specific attribute value from the environment handle. It returns YAC_SUCCESS to indicate success and YAC_ERROR to indicate failure.

Environment-level parameters will affect the behavior of all handles under the current environment information handle. Please configure related settings through the [yacSetEnvAttr](yacSetEnvAttr) function.

## Function Declaration

```c
YacResult yacGetEnvAttr(YacHandle hEnv,
                        YacEnvAttr attr,
                        YacVoid* value,
                        YacInt32 bufLength,
                        YacInt32* stringLength);
```

## Parameter Description

|Parameter Name |Description |
| ------------------ | ---------------------------- |
| hEnv (IN/OUT)      | Environment handle.             |
| attr (IN)          | Attribute type.                 |
| value (OUT)        | Stores the retrieved attribute value. |
| bufLength (OUT)    | Cache length of the retrieved attribute value. |
| stringLength (OUT) | Actual length of the retrieved attribute value. |
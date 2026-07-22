## Functionality Introduction

The OCILobLocatorIsInit function is used to determine whether a LOB is initialized.

## Function Declaration

```c
sword OCILobLocatorIsInit ( OCIEnv               *envhp,
                            OCIError             *errhp,
                            const OCILobLocator  *locp,
                            boolean              *is_initialized);
```

## Parameter Description

|Parameter Name |Description |
| ------ |--------------------------------------------|
| svchp (IN/OUT)    | Service context handle.                            |
| errhp (IN/OUT)    | An error handle that can retrieve diagnostic information when an error occurs. |
| locp (IN)         | The LOB locator that uniquely references the LOB. |
| is_initialized (OUT) | The result indicating whether it is initialized. |
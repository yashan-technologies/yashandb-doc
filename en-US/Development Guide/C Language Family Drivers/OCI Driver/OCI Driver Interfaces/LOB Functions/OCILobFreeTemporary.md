## Functionality Introduction

The OCILobFreeTemporary function is used to free temporary LOBs.

You must call the OCILobFreeTemporary function before calling the [OCIDescriptorFree](../Handle and Descriptor Functions/OCIDescriptorFree) function.

## Function Declaration

```c
sword OCILobFreeTemporary( OCISvcCtx          *svchp,
                           OCIError           *errhp,
                           OCILobLocator      *locp);
```

## Parameter Description

|Parameter Name |Description |
| -------------- | -------------------------------------------- |
| svchp (IN/OUT)  | Service context handle.                       |
| errhp (IN/OUT)  | An error handle that can provide diagnostic information in case of an error. |
| locp (IN/OUT)   | An internal LOB locator that uniquely references the LOB. |
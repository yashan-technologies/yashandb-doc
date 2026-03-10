## Functionality Introduction

The OCILogoff function is used to log off.

## Function Declaration

```c
sword   OCILogoff (OCISvcCtx *svchp, OCIError *errhp);
```

## Parameter Description

|Parameter Name |Description |
| -------------- | ------------------------------------------ |
| svchp (IN)      | Service context handle.                       |
| errhp (IN/OUT)  | An error handle that can retrieve diagnostic information when an error occurs. |
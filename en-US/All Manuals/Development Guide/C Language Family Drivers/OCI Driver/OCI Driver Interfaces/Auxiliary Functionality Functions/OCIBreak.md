## Functionality Introduction

The OCIBreak function is used to immediately execute an asynchronous interrupt.

## Function Declaration

```c
sword OCIBreak ( void       *hndlp,
                 OCIError   *errhp );
```

## Parameter Description

|Parameter Name |Description |
| --------- | -------------------------------------- |
| hndlp (IN/OUT) | Service context handle or server context handle. |
| errhp (IN/OUT) | Error handle, used to obtain diagnostic information when an error occurs. |
## Functionality Introduction

The OCIReset function is used to reset interrupted asynchronous operations and protocols.

## Function Declaration

```c
sword OCIReset ( void       *hndlp,
                 OCIError   *errhp );
```

## Parameter Description

|Parameter Name |Description |
| ---------- | ---------------------------------------- |
| hndlp (IN)     | Service context handle or server context handle.       |
| errhp (IN)     | Error handle that can be used to obtain diagnostic information when an error occurs. |
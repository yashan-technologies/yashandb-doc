## Functionality Introduction

The OCISessionPoolDestroy function is used to destroy a session pool.

## Function Declaration

```c
sword OCISessionPoolDestroy ( OCISPool     *spoolhp, 
                              OCIError     *errhp,
                              ub4           mode );
```

## Parameter Description

|Parameter Name |Description |
| --------------- | ------------------------------------------------------------ |
| spoolhp(IN/OUT)   | Session pool handle.                                       |
| errhp(IN/OUT)     | An error handle.<br />When an error occurs, it can be passed to [OCIErrorGet](../Auxiliary Functionality Functions/OCIErrorGet) to obtain diagnostic information. |
| mode(IN)          | Mode, reserved parameter.                                   |
## Functionality Introduction

The OCIStringAllocSize function is used to obtain the allocated memory size of a string, in bytes.

## Function Declaration

```c
sword OCIStringAllocSize ( OCIEnv              *env,
                           OCIError            *err, 
                           CONST OCIString     *vs,
                           ub4                 *allocsize ); 
```

## Parameter Description

|Parameter Name |Description |
| ---------------- |--------------------------------------------------------------------------------------|
| err (IN/OUT)       | Error handle.<br />If an error occurs, it is recorded in err, and this function returns OCI_error.<br />Diagnostic information can be obtained by calling [OCIErrorGet](../Auxiliary Functionality Functions/OCIErrorGet). |
| vs (IN)           | The string for which the allocated size is to be returned. vs must be a non-NULL pointer.                                                      |
| allocsize (OUT)   | Memory size.                                                                               |
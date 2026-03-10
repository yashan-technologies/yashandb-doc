## Functionality Overview

The OCIStringResize function is used to resize the memory of a string.

## Function Declaration

```c
sword OCIStringResize ( OCIEnv          *env,
                        OCIError        *err,
                        ub4             new_size,
                        OCIString       **str );
```

## Parameter Description

|Parameter Name |Description |
| ---------------- |-------------------------------------------------------------------|
| env (IN)          | Environment handle.<br />Its encoding setting is specified by mode, which is inherited from the statement handle derived from envhpp. |
| err (IN/OUT)      | An error handle.<br />In case of an error, it can be passed to [OCIErrorGet](../Auxiliary Functionality Functions/OCIErrorGet) to obtain diagnostic information. |
| new_size (IN)     | The new memory size for the string (in bytes). new_size must include space for the NULL character as the string terminator. |
| str (IN/OUT)      | The string to be freed from the OCI object buffer.                                               |
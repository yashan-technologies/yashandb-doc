## Functionality Overview

The OCIEnvNlsCreate function is used to create and initialize an environment handle.

## Function Declaration

```c
sword OCIEnvNlsCreate   ( OCIEnv        **envhpp,
                          ub4             mode,
                          void           *ctxp,
                          void          *(*malocfp)
                                             (void  *ctxp,
                                              size_t size),
                          void          *(*ralocfp)
                                             (void  *ctxp,
                                              void  *memptr,
                                              size_t newsize),
                          void          (*mfreefp)
                                             (void  *ctxp,
                                              void  *memptr))
                          size_t          xtramemsz,
                          void          **usrmempp
                          ub2             charset,
                          ub2             ncharset );
```

## Parameter Description

|Parameter Name |Description |
| -------------- | ------------------------------------------------------- |
| envhpp(OUT)      | Environment handle.<br />Its character set is set by mode, and this setting is inherited by statement handles derived from envhpp. |
| mode (IN)        | Specifies the initialization mode.                     |
| ctxp (IN)        | Specifies a user-defined context for memory callback routines, reserved parameter. |
| malocfp (IN)     | Specifies a user-defined memory allocation function, reserved parameter.             |
| ctxp (IN)        | Specifies a context pointer for the user-defined memory allocation function, reserved parameter. |
| size (IN)        | Specifies the size of memory to be allocated by the user-defined memory allocation function, reserved parameter. |
| ralocfp (IN)     | Specifies a user-defined memory reallocation function, reserved parameter.           |
| ctxp (IN)        | Specifies the context pointer for the user-defined memory reallocation function, reserved parameter. |
| memptr (IN)      | Pointer to the memory block, reserved parameter.      |
| newsize (IN)     | Specifies the new memory size to allocate, reserved parameter.    |
| mfreefp (IN)     | Specifies a user-defined memory deallocation function, reserved parameter.         |
| ctxp (IN)        | Specifies the context pointer for the user-defined memory deallocation function, reserved parameter. |
| memptr (IN)      | Pointer to the memory to be freed, reserved parameter. |
| xtramemsz (IN)   | Specifies the amount of user memory to allocate during the environment's lifespan, reserved parameter. |
| usrmempp (OUT)   | Returns a pointer, reserved parameter.<br />Points to the user memory allocated by the caller of size xtramemsz. |
| charset(IN)      | Client character set.                                |
| ncharset(IN)     | Client national character set, reserved parameter.    |

Some parameters currently only support the following values:

|Parameter |Valid Values |
| ------- | ------------------------------------------------ |
| mode      | OCI_THREADED (default value)<br />OCI_DEFAULT  <br /> OCI_NCHAR_LITERAL_REPLACE_ON <br /> OCI_NCHAR_LITERAL_REPLACE_OFF        |
| memptr  | NULL                                             |
| charset   | 0 (default value)<br />852 (GBK)<br />871 (UTF8) |
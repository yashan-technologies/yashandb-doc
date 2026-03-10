## Functionality Introduction

The OCIEnvCreate function is used to create and initialize an environment handle.

## Function Declaration

```c
sword OCIEnvCreate   ( OCIEnv       **envhpp,
                       ub4            mode,
                       const void    *ctxp,
                       const void    *(*malocfp)
                                      (void  *ctxp,
                                      size_t size),
                       const void    *(*ralocfp)
                                      (void  *ctxp,
                                      void  *memptr,
                                      size_t newsize),
                       const void     (*mfreefp)
                                      (void  *ctxp,
                                      void  *memptr))
                       size_t         xtramemsz,
                       void         **usrmempp );
```

## Parameter Description

|Parameter Name |Description |
| -------------- | ---------------------------------------------------------- |
| envhpp (OUT)     | The environment handle.<br />Its encoding settings are specified by mode, which are inherited by the statement handles derived from envhpp. |
| mode (IN)        | Specifies the initialization mode.                           |
| ctxp (IN)        | Specifies the user-defined context for memory callback routines, reserved parameter. |
| malocfp (IN)     | Specifies the user-defined memory allocation function, reserved parameter. |
| ctxp (IN)        | Specifies the context pointer for the user-defined memory allocation function, reserved parameter. |
| size (IN)        | Specifies the memory size to be allocated by the user-defined memory allocation function, reserved parameter. |
| ralocfp (IN)     | Specifies the user-defined memory reallocation function, reserved parameter. |
| ctxp (IN)        | Specifies the context pointer for the user-defined memory reallocation function, reserved parameter. |
| memptr (IN)      | Pointer to the memory block, reserved parameter.            |
| newsize (IN)     | Specifies the new memory size to be allocated, reserved parameter. |
| mfreefp (IN)     | Specifies the user-defined memory deallocation function, reserved parameter. |
| ctxp (IN)        | Specifies the context pointer for the user-defined memory deallocation function, reserved parameter. |
| memptr (IN)      | Pointer to the memory to be freed, reserved parameter.      |
| xtramemsz (IN)   | Specifies the amount of user memory to be allocated during the environment's lifetime, reserved parameter. |
| usrmempp (OUT)   | Returns a pointer, reserved parameter.<br />Points to the user memory allocated by the caller of xtramemsz size. |

Some parameters currently only support the following values:

|Parameter |Valid Values |
| ------ | --------------------------------------- |
| mode      | OCI_THREADED (default value)<br />OCI_DEFAULT <br /> OCI_NCHAR_LITERAL_REPLACE_ON <br /> OCI_NCHAR_LITERAL_REPLACE_OFF |
| memptr | NULL                                    |

## Functionality Introduction

The OCIInitialize function is used to initialize the environment handle.

## Function Declaration

```c
sword OCIInitialize ( ub4            mode,
                      const void    *ctxp, 
                      const void    *(*malocfp) 
                                     ( void   *ctxp,
                                        size_t size ),
                      const void    *(*ralocfp)
                                     ( void  *ctxp,
                                       void  *memptr,
                                       size_t newsize ),
                      const void    (*mfreefp)
                                     ( void  *ctxp,
                                       void  *memptr ));
```

## Parameter Description

|Parameter Name |Description |
| --------------- | ---------------------------------------------- |
| mode (IN)         | Specifies the initialization mode.             |
| ctxp (IN)         | Specifies a user-defined context for memory callback routines. |
| malocfp (IN)      | Specifies a user-defined memory allocation function. |
| ctxp (IN/OUT)     | Context pointer for the user-defined memory allocation function. |
| size (IN)         | Specifies the size of memory to be allocated by the user-defined memory allocation function. |
| ralocfp (IN)      | Specifies a user-defined memory reallocation function. |
| ctxp (IN/OUT)     | Context pointer for the user-defined memory reallocation function. |
| memptr (IN/OUT)   | Pointer to the memory block.                    |
| newsize (IN)      | Specifies the new size of memory to be allocated. |
| mfreefp (IN)      | Specifies a user-defined memory deallocation function. |
| ctxp (IN/OUT)     | Context pointer for the user-defined memory deallocation function. |
| memptr (IN/OUT)   | Pointer to the memory to be freed.              |

Some parameters currently support the following values:

|Parameter |Valid Values |
| ------ | ------------------------------------------------------------ |
| mode      | OCI_THREADED (default value)<br />OCI_DEFAULT<br />OCI_OBJECT<br />OCI_EVENTS |
| memptr | NULL                                                         |

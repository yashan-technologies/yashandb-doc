## Functionality Overview

If you select the OCI_DYNAMIC_FETCH mode in [OCIDefineByPos](OCIDefineByPos) or OCIDefineByPos2, you must call the OCIDefineDynamic function to set the corresponding additional attributes.

Currently, only the callback function returning OCI_CONTINUE is supported, and the value assigned to *piecep must be OCI_ONE_PIECE.

## Function Declaration

```c
sword OCIDefineDynamic ( OCIDefine   *defnp,
                         OCIError    *errhp,
                         void        *octxp, 
                         OCICallbackDefine      (ocbfp)(
                                  void          *octxp,
                                  OCIDefine     *defnp,
                                  ub4            iter, 
                                  void         **bufpp,
                                  ub4          **alenpp,
                                  ub1           *piecep,
                                  void         **indpp,
                                  ub2          **rcodep ) );
```

## Parameter Description

|Parameter Name |Description |
| -------------- | --------------------------------------------------------- |
| defnp (IN/OUT)   | Define structure handle.                              |
| errhp (IN/OUT)   | An error handle to retrieve diagnostic information when an error occurs. |
| octxp (IN)       | Pointer to the context of the callback function.     |
| (IN)             | Pointer to the callback function. It is called at runtime to obtain a pointer to the cache from which the retrieved data or part of it will be fetched. The callback also specifies the index, return code, as well as the length of the data pieces and indicators. |

Currently, some parameters only support the following values:

|Parameter |Valid Values |
| ----- | ------ |
| ocbfp     | Non-NULL     |

Currently, the output data cache does not support truncation of variable-length types. When retrieving dataset data, an error will occur if the output data cache size is insufficient for any variable-length types.
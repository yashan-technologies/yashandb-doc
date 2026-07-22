## Functionality Overview

The OCIBindDynamic function is used to register user callbacks for dynamic data allocation.

Currently, it only supports callbacks that return OCI_CONTINUE and only supports \*piecep assigned as OCI_ONE_PIECE.

## Function Declaration

```c
sword OCIBindDynamic ( OCIBind     *bindp,
                       OCIError    *errhp,
                       void        *ictxp, 
                       OCICallbackInBind         (icbfp)(
                                void             *ictxp,
                                OCIBind          *bindp,
                                ub4               iter, 
                                ub4               index, 
                                void            **bufpp,
                                ub4              *alenp,
                                ub1              *piecep, 
                                void            **indpp ),
                                void             *octxp,
                       OCICallbackOutBind        (ocbfp)(
                                void             *octxp,
                                OCIBind          *bindp,
                                ub4               iter, 
                                ub4               index, 
                                void            **bufpp, 
                                ub4             **alenpp,
                                ub1              *piecep,
                                void            **indpp, 
                                ub2             **rcodepp ) );
```

## Parameter Description

|Parameter Name |Description |
| -------------- |------------------------------|
| bindp (IN/OUT)   | Bind handle.                    |
| errhp (IN/OUT)   | An error handle that can retrieve diagnostic information when an error occurs. |
| ictxp (IN)       | Context pointer required by the callback function icbfp. |
| icbfp (IN)       | Callback function that returns pointers to the IN bind value or fragment at runtime. |
| octxp (IN)       | Context pointer required by the callback function ocbfp, reserved parameter, not currently used. |
| ocbfp (IN)       | Callback function that returns pointers to the OUT bind value or fragment at runtime, reserved parameter, not currently used. |

Some parameters currently only support the following values:

|Parameter |Valid Values |
| ----- |----------------|
| icbfp     | Non-NULL         |
| octxp     | NULL, reserved parameter, not currently used |
| ocbfp     | NULL, reserved parameter, not currently used |
## Functionality Introduction

The OCIParamGet function is used to return the descriptor of a parameter specified by the position in the descriptor handle or statement handle.

## Function Declaration

```c
sword OCIParamGet ( const void        *hndlp,
                    ub4                htype,
                    OCIError          *errhp,
                    void             **parmdpp,
                    ub4                pos );
```

## Parameter Description

|Parameter Name |Description |
| -------------- | ------------------------------------------------------------ |
| hndlp (IN)     | Statement handle or descriptor handle.<br />The OCIParamGet function returns the parameter descriptor for this handle. |
| htype (IN)     | The type of the handle passed in the hndlp parameter.       |
| errhp (IN/OUT) | An error handle.<br />It can be passed to [OCIErrorGet](../Auxiliary Functionality Functions/OCIErrorGet) to obtain diagnostic information when an error occurs. |
| parmdpp (OUT)  | The parameter descriptor at the position specified in the pos parameter, with a handle type of OCI_type_PARAM. |
| pos (IN)       | The position number in the statement handle or descriptor handle.<br />A parameter descriptor will be returned for this position. |

Some parameters currently only support the following values:

|Parameter |Valid Values |
| ----- | ----------------------------------- |
| htype | OCI_HTYPE_STMT<br />OCI_DTYPE_PARAM<br />OCI_HTYPE_ERROR |

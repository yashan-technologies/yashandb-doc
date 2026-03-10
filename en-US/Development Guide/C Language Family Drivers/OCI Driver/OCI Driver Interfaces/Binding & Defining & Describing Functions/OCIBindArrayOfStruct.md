## Functionality Introduction

The OCIBindArrayOfStruct function is used to set the skip parameters for static array binding. It is optional and can be used to set the increment for batch binding parameters.

## Function Declaration

```c
sword OCIBindArrayOfStruct ( OCIBind     *bindp,
                             OCIError    *errhp,
                             ub4          pvskip, 
                             ub4          indskip, 
                             ub4          alskip, 
                             ub4          rcskip );
```

## Parameter Description

|Parameter Name |Description |
| -------------- | ------------------------------------------ |
| bindp (IN/OUT)   | Handle for the binding structure.               |
| errhp (IN/OUT)   | An error handle that provides diagnostic information when an error occurs. |
| pvskip (IN)      | Skip parameter for the next data value.        |
| indskip (IN)     | Skip parameter for the next indicator value or structure. |
| alskip (IN)      | Skip parameter for the next actual length value. |
| rcskip (IN)      | Skip parameter for the next column level return code value. |

Some parameters currently only support the following values:

|Parameter Name |Valid Values |
| ------- | --------------- |
| pvskip         | Any value except 0  |
| indskip        | 0 or other reasonable values |
| alskip         | 0 or other reasonable values |
| rcskip         | 0 or other reasonable values |
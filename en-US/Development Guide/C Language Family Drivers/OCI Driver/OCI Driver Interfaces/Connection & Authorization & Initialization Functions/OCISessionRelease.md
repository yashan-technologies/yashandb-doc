## Functionality Introduction

The OCISessionRelease function is used to release a user session.

## Function Declaration

```c
sword OCISessionRelease ( OCISvcCtx       *svchp,
                          OCIError        *errhp,
                          OraText         *tag,
                          ub4              tag_len,
                          ub4              mode );
```

## Parameter Description

|Parameter Name |Description |
| ------------- | ------------------------------------------------------------ |
| svchp(IN)      | The handle of the service context.                          |
| errhp(IN/OUT)  | An error handle.<br />When an error occurs, it can be passed to [OCIErrorGet](../Auxiliary Functionality Functions/OCIErrorGet) to obtain diagnostic information. |
| tag (IN)       | Release identifier, reserved parameter.                     |
| tag_len(IN)    | Length of the release identifier, reserved parameter.       |
| mode(IN)       | Release mode, reserved parameter.                           |
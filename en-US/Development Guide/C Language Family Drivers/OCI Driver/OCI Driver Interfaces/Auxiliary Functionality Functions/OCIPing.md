## Functionality Overview

The OCIPing function is used to probe whether the server is abnormal.

## Function Declaration

```c
sword OCIPing ( OCISvcCtx     *svchp,
                OCIError      *errhp,
                ub4            mode );
```

## Parameter Description

|Parameter Name |Description |
| --------- | -------------------------------------- |
| svchp(IN)      | The handle of the initialized server context. |
| errhp(IN)      | Error handle, which can provide diagnostic information when an error occurs. |
| mode(IN)       | Mode, reserved parameter.                |
## Functionality Introduction

The OCIStringSize function is used to obtain the size of a string.

## Function Declaration

```c
ub4 OCIStringSize ( OCIEnv              *env,
                    CONST OCIString     *vs );
```

## Parameter Description

|Parameter Name |Description |
| ---------------- |-----------------------------------------------|
| env (IN)          | Environment handle.<br />The encoding setting is specified by mode, which is inherited from the statement handle derived from envhpp. |
| vs (IN)           | Pointer to the string.                          |
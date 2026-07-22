## Functionality Introduction

The OCIStringPtr function is used to obtain a pointer to a string.

## Function Declaration

```c
text *OCIStringPtr ( OCIEnv               *env,
                     CONST OCIString      *vs );
```

## Parameter Description

|Parameter Name |Description |
|-----------------|-----------------------------------------------|
| env (IN)            | Environment handle.<br />The encoding setting is specified by mode, which is inherited from the statement handle derived from envhpp. |
| vs (IN)             | Pointer to the string.                        |
## Functionality Introduction

The OCIStringAssignText function is used to assign a text string to a string.

## Function Declaration

```c
sword OCIStringAssignText ( OCIEnv           *env,
                            OCIError         *err, 
                            CONST OraText    *rhs, 
                            ub2              rhs_len,
                            OCIString        **lhs );
```

## Parameter Description

|Parameter Name |Description |
| ---------------- |------------------------------------------------------------------------|
| env (IN)         | Environment handle. <br /> The character set is set by mode, which is inherited from the statement handle derived from envhpp.  |
| err (IN/OUT)     | An error handle. <br /> It can be passed to [OCIErrorGet](../Auxiliary Functionality Functions/OCIErrorGet) to obtain diagnostic information when an error occurs. |
| rhs (IN)         | Source text string.                                                    |
| rhs_len (IN)     | Length of the source text string (in bytes).                          |
| lhs (IN/OUT)     | Target string.                                                         |
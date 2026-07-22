## Functionality Introduction

The OCILobIsEqual function is used to determine if two LOBs are equal.

## Function Declaration

```c
sword OCILobIsEqual ( OCIEnv                  *envhp,
                      const OCILobLocator     *x,
                      const OCILobLocator     *y,
                      boolean                 *is_equal );
```

## Parameter Description

|Parameter Name |Description |
| ------ |--------------------------------------------|
| envhp (IN)     | Environment handle.                           |
| x (IN)         | Internal LOB locator that uniquely references the LOB. |
| y (IN)         | Internal LOB locator that uniquely references the LOB. |
| is_equal (OUT) | Returns TRUE if the two LOBs are equal, otherwise returns FALSE. |
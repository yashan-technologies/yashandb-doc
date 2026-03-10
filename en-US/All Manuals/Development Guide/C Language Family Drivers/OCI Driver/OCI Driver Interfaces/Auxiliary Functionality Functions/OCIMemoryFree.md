## Functionality Overview

The OCIMemoryFree function is used to free memory.

## Function Declaration

```c
sword OCIMemoryFree ( void     *hndl, 
                      OCIError *err, 
                      void     *mem );
```

## Parameter Description

|Parameter Name |Description |
| ----------- |----------------------|
| hndl (IN)      | Environment handle.                          |
| err (IN)       | Error handle, which can provide diagnostic information when an error occurs. |
| mem (IN/OUT)   | Memory pointer.                              |
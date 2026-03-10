## Functionality Overview

The OCIMemoryAlloc function is used to allocate memory.

## Function Declaration

```c
sword OCIMemoryAlloc( void         *hndl, 
                      OCIError     *err, 
                      void        **mem, 
                      OCIDuration   dur, 
                      ub4           size, 
                      ub4           flags );
```

## Parameter Description

|Parameter Name |Description |
| --------- |----------------------|
| hndl(IN)       | Environment handle.           |
| err(IN)        | Error handle, used to obtain diagnostic information when an error occurs. |
| mem(OUT)       | Pointer to the allocated memory. |
| dur(IN)        | Memory lifecycle, reserved parameter. |
| size(IN)       | Length of memory to allocate. |
| flags(IN)      | Allocation mode, reserved parameter. |
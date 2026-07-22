In YashanDB C Driver, handles can be divided into three levels: 

1. Environment handle: Responsible for setting global environment variables, including character set, memory callback function registration, handle allocation and release, descriptor allocation and release, etc. This is the highest level handle.

2. Connection handle: Database connection handle, used to manage connections between the client and database server. Multiple connection handles can be created under an environment handle.

3. Statement handle: SQL statement handle, used to prepare and execute SQL statements. Multiple statement handles can be created under a connection handle.

In application development, the above hierarchical order should be followed for handle allocation and release. Among these, LOB descriptors, as special descriptors, must be allocated and released from the CONN handle using the [yacLobDescAlloc2](../LOB Functions/yacLobDescAlloc2) function.

|Interface Name |Interface Description |
|--------------------|--------------------------------------|
| [yacAllocHandle](yacAllocHandle) | Allocate space for the handle                   |
| [yacFreeHandle](yacFreeHandle)   | Release the space for the handle                |


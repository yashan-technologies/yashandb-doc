The C driver LOB functionality is implemented through the LOB (Large Object) data type, which supports the storage and retrieval of large data. The LOB data type is used to store large amounts of binary or character data, such as images, audio, video, documents, etc. In a database, LOBs are generally divided into two types: BLOB (Binary Large Object) and CLOB (Character Large Object).
- The BLOB type is used to store binary data, which can include files such as images, audio, and video.
- The CLOB type is used to store character data, which can include text files and XML files.

The C driver provides a set of API functions or methods to perform operations related to LOB data types, including inserting, updating (not applicable for ISC Distributed Cluster Deployment), deleting, and querying LOB data. When inserting or updating LOB data, API functions or methods can be used to write LOB data from files or memory caches to the database. To query or retrieve LOB data, API functions or methods can be used to read LOB data from the database and save it to files or memory caches.

When performing operations on LOB data, it is generally necessary to specify the LOB handle or identifier. The LOB handle is a reference pointing to LOB data. By specifying the LOB handle, one can obtain the length of the LOB data, read its contents, write data to the LOB, and more.

|Interface Name |Interface Description |
|-------------------|--------------------------------------|
| [yacLobGetChunkSize](yacLobGetChunkSize) | In the LOB protocol, get the chunk size of the LOB |
| [yacLobGetLength](yacLobGetLength)    | In the LOB protocol, get the total length of the LOB data           |
| [yacLobFreeTemporary](yacLobFreeTemporary) | In the LOB protocol, inform the server to release the temporary LOB |
| [yacLobIsTemporary](yacLobIsTemporary) | In the LOB protocol, determine if the LOB is a temporary LOB |
| [yacLobTrim](yacLobTrim) | In the LOB protocol, truncate the LOB data |
| [yacLobAppend](yacLobAppend) | In the LOB protocol, append one LOB to the end of another LOB |
| [yacLobCreateTemporary2](yacLobCreateTemporary2) | In the LOB protocol, inform the server to create a temporary LOB |
| [yacLobWriteAppend](yacLobWriteAppend) | In the LOB protocol, append data to the end of the LOB |
| [yacLobWriteAppendWithCharset](yacLobWriteAppendWithCharset) | In the LOB protocol, append data to the end of the LOB using a specific character set |
| [yacLobDescFree2](yacLobDescFree2) | In the LOB protocol, free space for the loblocator |
| [yacLobDescAlloc2](yacLobDescAlloc2) | In the LOB protocol, allocate space for the loblocator |
| [yacLobWrite2](yacLobWrite2) | In the LOB protocol, insert data into the LOB from a specified position |
| [yacLobWrite2WithCharset](yacLobWrite2WithCharset) | In the LOB protocol, insert data into the LOB from a specified position using a specific character set |
| [yacLobRead2](yacLobRead2) | In the LOB protocol, read LOB data from a specified position |
| [yacLobRead2WithCharset](yacLobRead2WithCharset) | In the LOB protocol, read LOB data from a specified position using a specific character set |
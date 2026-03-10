The OCI driver provides functionality to store and retrieve large data using the LOB data type. The LOB data type is used to store large amounts of binary or character data, such as images, audio, video, documents, etc. In the database, LOBs are generally divided into two types: BLOB (Binary Large Object) and CLOB (Character Large Object).
- BLOB type is used to store binary data and can store files like images, audio, videos, etc.
- CLOB type is used to store character data and can store text files, XML files, etc.

The OCI driver provides a set of API functions or methods to perform operations related to the LOB data type, including inserting, updating, deleting, and querying LOB data. When inserting or updating LOB data, API functions or methods can be used to write LOB data from a file or memory cache into the database. When querying or retrieving LOB data, API functions or methods can be used to read LOB data from the database and save it to a file or memory cache.

When performing operations on LOB data, it is generally necessary to specify a handle or identifier for the LOB. The LOB handle is a reference to the LOB data, and by specifying the LOB handle, one can obtain the length of the LOB data, read the content of the LOB data, write data to the LOB, etc.

|Interface Name |Interface Description |
|-------------------|--------------------------------------|
| [OCILobGetLength](OCILobGetLength)    |  Get the length of the LOB      |
| [OCILobRead](OCILobRead) | Read the LOB into cache  |
| [OCILobWrite](OCILobWrite) | Write cache to LOB  |
| [OCILobIsEqual](OCILobIsEqual) | Determine if two LOBs are equal |
| [OCILobCharSetForm](OCILobCharSetForm) | Get the character set form of the LOB |
| [OCILobCharSetId](OCILobCharSetId) | Get the character set ID of the LOB |
| [OCILobGetChunkSize](OCILobGetChunkSize) | Get the chunk size of the LOB  |
| [OCILobCreateTemporary](OCILobCreateTemporary) | Create a temporary LOB |
| [OCILobIsTemporary](OCILobIsTemporary) | Determine if the LOB is temporary |
| [OCILobFreeTemporary](OCILobFreeTemporary) | Free the temporary LOB |
| [OCILobRead2](OCILobRead2) | Read the LOB into cache  |
| [OCILobGetLength2](OCILobGetLength2) | Get the length of the LOB |
| [OCILobOpen](OCILobOpen) | Open the LOB in the specified mode |
| [OCILobClose](OCILobClose) | Close the previously opened LOB |
| [OCILobWrite2](OCILobWrite2) | Write cache to LOB |
| [OCILobTrim2](OCILobTrim2) | Truncate the LOB value to a shorter length |
| [OCILobLocatorIsInit](OCILobLocatorIsInit) | Determine if the LOB is initialized |
| [OCILobIsOpen](OCILobIsOpen) | Determine if the LOB is open |
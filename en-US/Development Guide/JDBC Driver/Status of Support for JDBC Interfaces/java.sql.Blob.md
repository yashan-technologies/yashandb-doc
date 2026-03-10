The Blob interface is used to manipulate BLOB type data.

The YashanDB JDBC driver supports the following functionalities for the BLOB interface:

|Return Type |Method |Remarks |
|----------------------|------------------|---------------|
| long                 | length()         |           -  |
| byte[]               | getBytes(long pos, int length)         |   -          |
| int                  | setBytes(long pos, byte[] bytes)       |     -        |
| int                  | setBytes(long pos, byte[] bytes, int offset, int len) | This interface can only be called in ISC Distributed Cluster Deployment to write data into the temporary LOB on the CN node and cannot write data to the DN node. |
| java.io.OutputStream | setBinaryStream(long pos)      |   -          |
| java.io.InputStream  | getBinaryStream() |   -          |
| java.io.InputStream  | getBinaryStream(long pos, long length)    | -            |
| void                 | free()             |  -           |
| void                 | void truncate(long len)                      | Not applicable in ISC Distributed Cluster Deployment.                                                    |

Unsupported functionalities:

|Return Type |Method |
|-------|---------------------------------------|
| long  | position(byte pattern[], long start)  |
| long  | position(Blob pattern, long start)    |

Blob接口用于操作BLOB类型数据。

YashanDB JDBC驱动对BLOB接口已支持功能：

|  返回类型| 方法| 备注|
|----------------------|------------------|---------------|
| long                 | length()         |           -  |
| byte[]               | getBytes(long pos, int length)         |   -          |
| int                  | setBytes(long pos, byte[] bytes)       |     -        |
| int                  | setBytes(long pos, byte[] bytes, int offset, int len) | 存算一体分布式集群部署中只能调用本接口将数据写入至CN节点的临时LOB中，无法将数据写至DN节点上。 |
| java.io.OutputStream | setBinaryStream(long pos)      |   -          |
| java.io.InputStream  | getBinaryStream() |   -          |
| java.io.InputStream  | getBinaryStream(long pos, long length)    | -            |
| void                 | free()             |  -           |
| void                 | void truncate(long len)          | 不适用于存算一体分布式集群部署。       |

未支持功能：

|  返回类型| 方法|
|-------|---------------------------------------|
| long  | position(byte pattern[], long start)  |
| long  | position(Blob pattern, long start)    |

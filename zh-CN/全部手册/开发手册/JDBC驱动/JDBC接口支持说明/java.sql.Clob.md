Clob接口用于操作CLOB类型数据。

YashanDB JDBC驱动对CLOB接口已支持功能（单机部署/共享集群部署）：

|  返回类型| 方法|
|----------------------|------------------------------------------------------|
| long                 | length()                                             |
| String               | getSubString(long pos, int length)                   |
| int                  | setString(long pos, String str)                      |
| int                  | setString(long pos, String str, int offset, int len) |
| java.io.OutputStream | setAsciiStream(long pos)                             |
| java.io.Writer       | setCharacterStream(long pos)                         |
| java.io.Reader       | getCharacterStream()                                 |
| Reader               | getCharacterStream(long pos, long length)            |
| java.io.InputStream  | getAsciiStream()                                     |
| void                 | free()                                               |
| void                 | truncate(long len)                                   |

YashanDB JDBC驱动对CLOB接口已支持功能（存算一体分布式集群部署）：

|  返回类型| 方法| 备注|
|----------------------|------------------------------------------------------|------------------------------------------------|
| long                 | length()                                             |  -                                              |
| String               | getSubString(long pos, int length)                   | -                                               |
| int                  | setString(long pos, String str)                      | -                                               |
| int                  | setString(long pos, String str, int offset, int len) | 存算一体分布式集群部署中只能调用本接口将数据写入至CN节点的临时LOB中，无法将数据写至DN节点上。 |
| java.io.Reader       | getCharacterStream()                                 | -                                               |
| java.io.InputStream  | getAsciiStream()                                     | -                                               |
| java.io.OutputStream | setAsciiStream(long pos)                             | -                                               |
| java.io.Writer       | setCharacterStream(long pos)                         | -                                               |
| Reader               | getCharacterStream(long pos, long length)            | -                                               |
| void                 | truncate(long len)                                   | 存算一体分布式集群部署中只能调用本接口将数据写入至CN节点的临时LOB中，无法将数据写至DN节点上。 |
| void                 | free()                                               | -                                               |

未支持功能（单机部署/共享集群部署）：

|  返回类型| 方法|
|-------|------------------------------------------|
| long  | position(String searchstr, long start)   |
| long  | position(Clob searchstr, long start)     |

未支持功能（存算一体分布式集群部署）：

|  返回类型| 方法|
|-------|-----------------------------------------|
| long  | position(String searchstr, long start)  |
| long  | position(Clob searchstr, long start)    |

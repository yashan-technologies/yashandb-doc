The Clob interface is used to handle CLOB type data.

The YashanDB JDBC driver supports the CLOB interface functionality (Standalone Deployment / YAC/Distributed Cluster Deployment):

|Return Type |Method |
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

The YashanDB JDBC driver supports the CLOB interface functionality (ISC Distributed Cluster Deployment):

|Return Type |Method |Remarks |
|----------------------|------------------------------------------------------|------------------------------------------------|
| long                 | length()                                             |  -                                              |
| String               | getSubString(long pos, int length)                   | -                                               |
| int                  | setString(long pos, String str)                      | -                                               |
| int                  | setString(long pos, String str, int offset, int len) | In ISC Distributed Cluster Deployment, this method can only be used to write data to the temporary LOB on CN nodes, not to write data on DN nodes. |
| java.io.Reader       | getCharacterStream()                                 | -                                               |
| java.io.InputStream  | getAsciiStream()                                     | -                                               |
| java.io.OutputStream | setAsciiStream(long pos)                             | -                                               |
| java.io.Writer       | setCharacterStream(long pos)                         | -                                               |
| Reader               | getCharacterStream(long pos, long length)            | -                                               |
| void                 | truncate(long len)                                | In ISC Distributed Cluster Deployment, this method can only be used to write data to the temporary LOB on CN nodes, not to write data on DN nodes. |
| void                 | free()                                               | -                                               |

Unsupported functionality (Standalone Deployment / YAC/Distributed Cluster Deployment):

|Return Type |Method |
|-------|------------------------------------------|
| long  | position(String searchstr, long start)   |
| long  | position(Clob searchstr, long start)     |

Unsupported functionality (ISC Distributed Cluster Deployment):

|Return Type |Method |
|-------|-----------------------------------------|
| long  | position(String searchstr, long start)  |
| long  | position(Clob searchstr, long start)    |

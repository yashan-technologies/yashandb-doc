Driver接口主要用于识别可连接的URL。

YashanDB JDBC驱动对Driver接口已支持功能：

| 返回类型 | 方法 |
| --- | --- |
| Connection | connect(String url, java.util.Properties info) |
| boolean | acceptsURL(String url)  |
| DriverPropertyInfo\[\] | getPropertyInfo(String url,java.util.Properties info)  |
| int | getMajorVersion() |
| int | getMinorVersion() |
| boolean | jdbcCompliant() |

未支持功能：

| 返回类型 | 方法 |
| --- | --- |
| Logger | getParentLogger() |
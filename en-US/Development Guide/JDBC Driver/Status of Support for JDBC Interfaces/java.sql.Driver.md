The Driver interface is primarily used to identify connectable URLs.

The YashanDB JDBC driver supports the following functionalities for the Driver interface:

|Return Type |Method |
| --- | --- |
| Connection | connect(String url, java.util.Properties info) |
| boolean | acceptsURL(String url)  |
| DriverPropertyInfo\[\] | getPropertyInfo(String url,java.util.Properties info)  |
| int | getMajorVersion() |
| int | getMinorVersion() |
| boolean | jdbcCompliant() |

Unsupported functionalities:

|Return Type |Method |
| --- | --- |
| Logger | getParentLogger() |

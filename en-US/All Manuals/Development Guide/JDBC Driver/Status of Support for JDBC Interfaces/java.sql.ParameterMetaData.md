The ParameterMetadata interface is used to obtain type and attribute information of the bound parameters in PreparedStatement.

The YashanDB JDBC driver supports the following functionality for the ParameterMetaData interface:

|Return Type |Method |
|--------|---------------------------------|
| int    | getParameterCount()             |
| int    | getPrecision(int param)         |
| int    | getScale(int param)             |
| int    | getParameterType(int param)     |
| String | getParameterTypeName(int param) |

The following functionality is not supported:

|Return Type |Method |
|---------|----------------------------------|
| boolean | isSigned(int param)              |
| int     | isNullable(int param)            |
| String  | getParameterClassName(int param) |
| int     | getParameterMode(int param)      |

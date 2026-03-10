ParameterMetadata接口用于获取PreparedStatement中绑定参数的类型与属性信息。

YashanDB JDBC驱动对ParameterMetaData接口已支持功能：

|  返回类型| 方法|
|--------|---------------------------------|
| int    | getParameterCount()             |
| int    | getPrecision(int param)         |
| int    | getScale(int param)             |
| int    | getParameterType(int param)     |
| String | getParameterTypeName(int param) |

未支持功能：

|  返回类型| 方法|
|---------|----------------------------------|
| boolean | isSigned(int param)              |
| int     | isNullable(int param)            |
| String  | getParameterClassName(int param) |
| int     | getParameterMode(int param)      |

YashanDB JDBC在配置连接时提供语法兼容模式设置，在数据库服务端为mysql模式时，相应地将连接配置为mysql模式，可以实现驱动程序按MySQL兼容模式进行如下操作处理：

- 传入的用户名会区分大小写。

- 元数据DatabaseMetaData的getDatabaseProductName接口的返回值会变成mysql。

数据库服务端为yashan模式时，无需进行兼容模式配置，或配置为yashan。

相关参数如下：

|  参数| 类型| 描述|
| ------------------------- | ------------------------ |-------------------------------|
| compatVector | string | 兼容模式设置，只能设置为mysql和yashan。 |

URL连接配置示例：

```java
jdbc:yasdb://192.168.1.2:1688/schema_name?compatVector=mysql
```

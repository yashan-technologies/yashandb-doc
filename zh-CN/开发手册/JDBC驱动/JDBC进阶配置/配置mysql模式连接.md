YashanDB JDBC在配置连接时提供语法兼容模式设置，在数据库服务端为mysql模式时，相应地将连接配置为mysql模式，可以实现驱动程序按MySQL兼容模式进行如下操作处理：

- 传入的用户名会区分大小写。

- 元数据DatabaseMetaData的getDatabaseProductName接口的返回值会变成mysql。

数据库服务端为yashan模式时，无需进行兼容模式配置，或配置为yashan。 

YashanDB JDBC驱动在URL中支持部分常用参数及其说明如下：

|  参数| 类型| 描述|
| ------------------------- | ------------------------ |-------------------------------|
| compatVector | string | 兼容模式设置，只能设置为mysql和yashan。 |
| connectTimeout | string | 建立连接时，创建socket连接的超时时间，单位为s。不配置时默认为10s。 |
| socketTimeout | string | 获取connection后，TCP通信过程中，客户端等待服务端返回数据的超时时间，单位为s。不配置时默认为0s，一直等待。 |
| loginTimeout | string | 创建socket成功后，进行登录认证时，客户端等待服务端返回数据的超时时间，单位为s。不配置时默认为300s。 |
| serverMode | string | 指定连接模式，可选值为shared（共享服务器模式，默认值）和dedicated（专用服务器模式）。 |
| autoCommit | string | 是否开启自动提交事务，TRUE表示开启，FALSE表示不开启，默认为TRUE。在mysql模式下，是否自动提交事务还受数据库服务端系统变量AUTOCOMMIT控制。 |
| mapDateToTimestamp | string | 该参数表示日期转时间类型是否开启，TRUE表示开启，FALSE表示不开启，默认为FALSE。 |
| allowMultiStmt | string | 是否开启支持多SQL，TRUE表示开启，FALSE表示不开启，默认为FALSE。开启后可同时传入多条SQL语句，SQL语句间用分号隔开。 |

示例：

```java
// URL连接配置
jdbc:yasdb://192.168.1.2:1688/schema_name?compatVector=mysql

//URL多参数配置：
// 配置mysql模式连接，同时设置连接超时和socket超时
jdbc:yasdb://192.168.1.2:1688/schema_name?compatVector=mysql&connectTimeout=60&socketTimeout=120
```

> **Note**:
>
>  在mysql模式下，事务自动提交行为由客户端参数autoCommit和数据库服务端系统变量AUTOCOMMIT共同决定。若服务端未开启自动提交但客户端开启，最终表现仍为不自动提交。建议将服务端配置与客户端配置的自动提交配置保持一致。


YashanDB JDBC驱动支持以下UDT类型的查询和参数绑定：

- Object
- Varray
- Nested Table

在查询和参数绑定时，服务端的类型与Java类型的对照关系如下所示。

|  服务端类型| Object| Varray| Nested Table|
|-----------------|------------------|-----------------|------------------|
| 查询和参数绑定的java类型  | java.sql.Struct  | java.sql.Array  | java.sql.Array   |

此外，JDBC还支持用户自定义java类型与服务端Object类型的映射，见[SQLData自定义类与UDT类型映射](java.sql.SQLData)。

>**Note**:
> 
> UDT相关功能不适用于存算一体分布式集群部署。

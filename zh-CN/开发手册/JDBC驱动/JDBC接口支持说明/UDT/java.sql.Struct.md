Struct用来表示数据库中的UDT Object类型的数据。

对象方法：

|  返回类型| 方法| 备注|
|----------|------------------------------------------|------------------------------------|
| String   | getSQLTypeName()                         | 返回UDT类型全称。                         |
| Object[] | getAttributes()                          | 返回UDT类型数据的Object[]对象。              |
| Object[] | getAttributes(Map<String, Class<?>> map) | 添加UDT映射SQLData对象集合，返回对应的SQLData对象。 |
| void     | setAttributes(Object[] attributes)       | 设置UDT类型数据。                         |

Struct使用示例：

```java
ResultSet resultSet = statement.executeQuery("select * from udt_tab");

while (resultSet.next()) {
    Struct struct = (Struct) resultSet.getObject(1);
    String udtName = struct.getSQLTypeName();
    Object[] attrs = struct.getAttributes();
}
resultSet.close();
```

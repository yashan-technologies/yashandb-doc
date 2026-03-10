Array用来表示数据库中的Array和Nested Table类型的数据。

包含以下方法：

| 返回类型      | 方法                                                             | 备注                                                                           |
|-----------|----------------------------------------------------------------|------------------------------------------------------------------------------|
| boolean   | isFreed()                                                      | 返回Array对象是否已经释放。                                                             |
| void      | free()                                                         | 释放Array对象。                                                                   |
| void      | setElementTypeCode(int elementTypeCode)                        | 设置Array数据类型的类型码，并设置对应的类型名称。                                                  |
| void      | setElements(Object[] elements)                                 | 设置Array数据。                                                                   |
| void      | setElementTypeName(String elementTypeName)                     | 设置数据类型名称。                                                                    |
| String    | getBaseTypeName()                                              | 返回Array数据类型名称。                                                               |
| int       | getBaseType()                                                  | 返回Array数据类型码。                                                                |
| Object    | getArray()                                                     | 返回Array数据类型。                                                                 |
| Object    | getArray(Map<String, Class\<?>> map)                            | 添加UDT类型映射集合，对Array数据进行解析映射，返回对应的数据对象。                                        |
| Object    | getArray(long index, int count)                                | 返回 `[index, index + count)` 范围的数据。                                           |
| Object    | getArray(long index, int count, Map<String, Class\<?>> map)     | 添加UDT类型映射集合，对Array数据进行解析映射，返回 `[index, index + count)` 范围的数据。                |
| ResultSet | getResultSet()                                                 | 返回Array数据的ResultSet结果集，ResultSet结果集有两列，第一列为数据在原Array中的index，从1开始，第二列为真实数据。   |
| ResultSet | getResultSet(Map<String, Class\<?>> map)                        | 添加UDT类型映射集合，返回Array数据的ResultSet结果集，ResultSet列信息同上。                           |
| ResultSet | getResultSet(long index, int count)                            | 返回 `[index, index + count)` 范围的数据对应的ResultSet结果集，ResultSet列信息同上。             |
| ResultSet | getResultSet(long index, int count, Map<String, Class\<?>> map) | 添加UDT类型映射集合，返回 `[index, index + count)` 范围的数据对应的ResultSet结果集，ResultSet列信息同上。 |

Array使用示例：

```java
ResultSet resultSet = statement.executeQuery("select * from udt_tab");

while(resultSet.next()){
    Array array = resultSet.getArray(1);

    String typeName = array.getBaseTypeName();
    int typeCode = array.getBaseType();
    // 获取UDT Array的数据。
    Object[] element = array.getArray();
    // 把UDT Array的数据封装成ResultSet的形式供后续读取。
    ResultSet rs = array.getResultSet();
}
```

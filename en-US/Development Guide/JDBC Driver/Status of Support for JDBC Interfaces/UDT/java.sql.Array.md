Array is used to represent the Array and Nested Table types of data in the database.

It includes the following methods:

|Return Type |Method |Notes |
|-----------|----------------------------------------------------------------|------------------------------------------------------------------------------|
| boolean     | isFreed()                                                    | Returns whether the Array object has been freed.                               |
| void        | free()                                                       | Frees the Array object.                                                        |
| void        | setElementTypeCode(int elementTypeCode)                      | Sets the type code of the Array data type and the corresponding type name.     |
| void        | setElements(Object[] elements)                               | Sets the Array data.                                                           |
| void        | setElementTypeName(String elementTypeName)                   | Sets the data type name.                                                       |
| String      | getBaseTypeName()                                            | Returns the name of the Array data type.                                       |
| int         | getBaseType()                                                | Returns the type code of the Array data type.                                  |
| Object      | getArray()                                                   | Returns the Array data type.                                                   |
| Object      | getArray(Map<String, Class<?>> map)                          | Adds a UDT type mapping collection, parses and maps the Array data, and returns the corresponding data object. |
| Object      | getArray(long index, int count)                              | Returns the data in the range of `[index, index + count)`.                    |
| Object      | getArray(long index, int count, Map<String, Class<?>> map)   | Adds a UDT type mapping collection, parses and maps the Array data, and returns the data in the range of `[index, index + count)`. |
| ResultSet   | getResultSet()                                               | Returns the ResultSet of the Array data, which has two columns: the first column is the index of the data in the original Array, starting from 1, and the second column is the actual data. |
| ResultSet   | getResultSet(Map<String, Class<?>> map)                      | Adds a UDT type mapping collection and returns the ResultSet of the Array data, with the same column information as above. |
| ResultSet   | getResultSet(long index, int count)                          | Returns the ResultSet of the data corresponding to the range `[index, index + count)`, with the same column information as above. |
| ResultSet   | getResultSet(long index, int count, Map<String, Class<?>> map) | Adds a UDT type mapping collection and returns the ResultSet of the data corresponding to the range `[index, index + count)`, with the same column information as above. |

Example of using Array:

```java
ResultSet resultSet = statement.executeQuery("select * from udt_tab");

while(resultSet.next()){
    Array array = resultSet.getArray(1);

    String typeName = array.getBaseTypeName();
    int typeCode = array.getBaseType();
    // Get the UDT Array data.
    Object[] element = array.getArray();
    // Wrap the UDT Array data into a ResultSet format for subsequent reading.
    ResultSet rs = array.getResultSet();
}
```

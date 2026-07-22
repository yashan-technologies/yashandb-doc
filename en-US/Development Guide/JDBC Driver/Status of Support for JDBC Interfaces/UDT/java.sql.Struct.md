Struct is used to represent UDT Object types in the database.

Object Methods:

|Return Type |Method |Note |
|----------|------------------------------------------|------------------------------------|
| String      | getSQLTypeName()                          | Returns the full name of the UDT type. |
| Object[]    | getAttributes()                           | Returns an Object[] representing the UDT data. |
| Object[]    | getAttributes(Map<String, Class<?>> map) | Adds a mapping of SQLData objects for UDT and returns the corresponding SQLData objects. |
| void        | setAttributes(Object[] attributes)        | Sets the UDT type data.            |

Example of using Struct:

```java
ResultSet resultSet = statement.executeQuery("select * from udt_tab");

while (resultSet.next()) {
    Struct struct = (Struct) resultSet.getObject(1);
    String udtName = struct.getSQLTypeName();
    Object[] attrs = struct.getAttributes();
}
resultSet.close();
```

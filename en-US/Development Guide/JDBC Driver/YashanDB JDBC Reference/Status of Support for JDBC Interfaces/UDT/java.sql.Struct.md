## Description

The Struct interface represents that it is used to represent UDT Object types in the database.

`java.sql.Struct` represents that it is the core interface in the JDBC specification for mapping database structured types (SQL STRUCT types). It acts as a direct mapping object for database structured types in Java programs. The `STRUCT` type in a database (essentially a collection of named fields, for example, structured data containing `id`, `name` and `age`) is encapsulated into a `Struct` instance after being queried via JDBC. Developers can obtain the name of the structured type and the array of field values through the methods of this interface, thus realizing the reading and manipulation of database structured data.


Detailed interface specifications, please refer to [The JDBC Official Website](https://docs.oracle.com/en/java/javase/17/docs/api/java.sql/java/sql/Struct.html).

## Method

The methods included in `java.sql.Struct` are as follows:

|Method |Return Type |Notes |
|------------------------------------------|----------|------------------------------------|
| getSQLTypeName()                          | String      | Returns the full name of the UDT type. |
| getAttributes()                           | Object[]    | Returns an Object[] representing the UDT data. |
| getAttributes(Map<String, Class<?>> map) | Object[]    | Adds a mapping of SQLData objects for UDT and returns the corresponding SQLData objects. |
| setAttributes(Object[] attributes)        | void        | Sets the UDT type data.            |

## Example

```java
package quickstart;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.sql.Struct;
import java.util.Properties;

public class StructExample {

    public static Connection getConnection() throws SQLException {
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");
        Class.forName("com.yashandb.jdbc.Driver");
        return DriverManager.getConnection(url, info);
    }

    // Read Struct data
    public void readStructData(Connection conn) throws SQLException {
        try (Statement stmt = conn.createStatement()) {
            try (ResultSet resultSet = stmt.executeQuery("SELECT * FROM udt_tab")) {
                while (resultSet.next()) {
                    Struct struct = (Struct) resultSet.getObject(1);
                    String udtName = struct.getSQLTypeName();
                    Object[] attrs = struct.getAttributes();
                    System.out.println("UDT Type: " + udtName + ", Attributes: " + attrs.length);
                }
            }
        }
    }
}
```

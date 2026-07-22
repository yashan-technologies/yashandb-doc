## Description

The Array interface represents that it is used to represent the Array and Nested Table types of data in the database.

`java.sql.Array` represents that it is a core interface in the JDBC specification for mapping database array types. Its core functions are to read array-type fields defined in a database into objects operable by Java programs and to write arrays in Java to array-type fields in a database.


Detailed interface specifications, please refer to [The JDBC Official Website](https://docs.oracle.com/en/java/javase/17/docs/api/java.sql/java/sql/Array.html).

## Method

java.sql.Array includes the following methods:

|Method |Return Type |Notes |
|----------------------------------------------------------------|-----------|------------------------------------------------------------------------------|
| isFreed()                                                    | boolean     | Returns whether the Array object has been freed.                               |
| free()                                                       | void        | Frees the Array object.                                                        |
| setElementTypeCode(int elementTypeCode)                      | void        | Sets the type code of the Array data type and the corresponding type name.     |
| setElements(Object[] elements)                               | void        | Sets the Array data.                                                           |
| setElementTypeName(String elementTypeName)                   | void        | Sets the data type name.                                                       |
| getBaseTypeName()                                            | String      | Returns the name of the Array data type.                                       |
| getBaseType()                                                | int         | Returns the type code of the Array data type.                                  |
| getArray()                                                   | Object      | Returns the Array data type.                                                   |
| getArray(Map<String, Class<?>> map)                          | Object      | Adds a UDT type mapping collection, parses and maps the Array data, and returns the corresponding data object. |
| getArray(long index, int count)                              | Object      | Returns the data in the range of `[index, index + count)`.                    |
| getArray(long index, int count, Map<String, Class<?>> map)   | Object      | Adds a UDT type mapping collection, parses and maps the Array data, and returns the data in the range of `[index, index + count)`. |
| getResultSet()                                               | ResultSet   | Returns the ResultSet of the Array data, which has two columns: the first column is the index of the data in the original Array, starting from 1, and the second column is the actual data. |
| getResultSet(Map<String, Class<?>> map)                      | ResultSet   | Adds a UDT type mapping collection and returns the ResultSet of the Array data, with the same column information as above. |
| getResultSet(long index, int count)                          | ResultSet   | Returns the ResultSet of the data corresponding to the range `[index, index + count)`, with the same column information as above. |
| getResultSet(long index, int count, Map<String, Class<?>> map) | ResultSet   | Adds a UDT type mapping collection and returns the ResultSet of the data corresponding to the range `[index, index + count)`, with the same column information as above. |

## Example

```java
package quickstart;

import java.sql.Array;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class ArrayExample {

    public static Connection getConnection() throws SQLException {
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        String user = "sales";
        String password = "sales";
        try {
            Class.forName("com.yashandb.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            throw new SQLException("Driver not found", e);
        }
        return DriverManager.getConnection(url, user, password);
    }

    // Read Array data
    public static void readArrayData(Connection conn) throws SQLException {
        // Create test table and data first
        try (Statement stmt = conn.createStatement()) {
            // Create array type (check if exists)
            try {
                stmt.execute("DROP TABLE IF EXISTS udt_tab");
                stmt.execute("DROP TYPE IF EXISTS int_array");
                stmt.execute("CREATE TYPE int_array AS VARRAY(10) OF INT");
            } catch (SQLException e) {
                try {
                    stmt.execute("CREATE TYPE int_array AS VARRAY(10) OF INT");
                } catch (SQLException e2) {
                    System.out.println("Type info: " + e2.getMessage());
                }
            }

            // Create table (use IF NOT EXISTS)
            try {
                stmt.execute("CREATE TABLE IF NOT EXISTS udt_tab (id INT, arr int_array)");
            } catch (SQLException e) {
                System.out.println("Table info: " + e.getMessage());
            }

            // Insert test data (delete first, handle unique constraint)
            try {
                stmt.execute("INSERT INTO udt_tab VALUES (1, int_array(1, 2, 3, 4, 5))");
                stmt.execute("INSERT INTO udt_tab VALUES (2, int_array(10, 20, 30))");
                conn.commit();
            } catch (SQLException e) {
                System.out.println("Insert info: " + e.getMessage());
                conn.rollback();
            }
        } catch (SQLException e) {
            conn.rollback();
            throw e;
        } finally {
        }

        // Read Array data
        try (Statement stmt = conn.createStatement();
             ResultSet resultSet = stmt.executeQuery("select * from udt_tab")) {

            while (resultSet.next()) {
                int id = resultSet.getInt(1);
                Array array = resultSet.getArray(2);
                if (array != null) {
                    String typeName = array.getBaseTypeName();
                    int typeCode = array.getBaseType();
                    // Get the UDT Array data
                    Object[] element = (Object[]) array.getArray();
                    // Wrap the UDT Array data into a ResultSet format for subsequent reading
                    try (ResultSet rs = array.getResultSet()) {
                        System.out.println("ID: " + id + ", Array elements:");
                        while (rs.next()) {
                            // Process array elements
                            Object value = rs.getObject(2);
                            System.out.println("  - " + value);
                        }
                    }
                }
            }
        }
    }

    public static void main(String[] args) {
        try (Connection conn = getConnection()) {
            readArrayData(conn);
            System.out.println("Array data read successfully");
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
```

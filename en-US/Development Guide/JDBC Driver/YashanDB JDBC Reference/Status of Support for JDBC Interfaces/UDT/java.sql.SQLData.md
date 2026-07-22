## Description

`java.sql.SQLData` interface represents that it is used to create custom classes and map them to the UDT's Object type. Its core function is to allow developers to define custom mapping rules between Java classes and database user-defined types (such as SQL `STRUCT` types, Oracle object types, and PostgreSQL composite types). A Java class that implements this interface can convert the fields of a database user-defined type into the attributes of a Java object and also write the attributes of a Java object back to the fields of a database user-defined type, serving as the standard method for handling conversions between complex database user-defined types and Java objects.


Detailed interface specifications, please refer to [The JDBC Official Website](https://docs.oracle.com/en/java/javase/17/docs/api/java.sql/java/sql/SQLData.html).

## Method

The methods included in java.sql.SQLData are as follows:

|Method |Return Type |Notes |
|--------------------------------------------|--------|------------------------------------|
| getSQLTypeName()                             | String      | Returns the corresponding UDT type name, which must be in the schema.name full name. |
| readSQL (SQLInput stream, String typeName)  | void        | Reads each property or element of the SQL type from the provided input stream and sets the UDT type name. |
| writeSQL (SQLOutput stream)                  | void        | Writes each property of the SQL type to the provided output stream. |

>**Note**:
> 
> SQLData requires users to implement it in custom classes. Only by correctly implementing the three methods in SQLData can the mapping between UDT types and custom classes be completed.

## Example

### Creating UDT

```sql
-- The definition of UDT type and table structure is as follows:
create or replace type udt_object as object(age int, name varchar(32));

create table udt_table(id int, person udt_object);
```

```java
package quickstart;

import java.sql.SQLData;
import java.sql.SQLInput;
import java.sql.SQLOutput;
import java.sql.SQLException;

// Custom Java type
public class TypeHolder implements SQLData {
    private int age;
    private String name;
    private String typeName;

    public void setAge(int age) {
        this.age = age;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public String getName() {
        return name;
    }

    @Override
    public String getSQLTypeName() throws SQLException {
        return this.typeName;
    }

    @Override
    public void readSQL(SQLInput input, String typeName) throws SQLException {
        this.typeName = typeName;
        this.age = input.readInt();
        this.name = input.readString();
    }

    @Override
    public void writeSQL(SQLOutput output) throws SQLException {
        output.writeInt(this.age);
        output.writeString(this.name);
    }
}
```

### UDT Parameter Binding

To use UDT for parameter binding, instantiate the custom class first, then call the `setObject` method for binding.

```java
package quickstart;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.SQLException;
import java.util.Properties;

public class UDTParameterBindingExample {

    public static Connection getConnection() throws SQLException {
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");
        Class.forName("com.yashandb.jdbc.Driver");
        return DriverManager.getConnection(url, info);
    }

    // UDT parameter binding example
    public void bindUDTParameter(Connection conn) throws SQLException {
        PreparedStatement preparedStatement = conn.prepareStatement("insert into udt_table values(?, ?)");
        preparedStatement.setInt(1, 1);
        TypeHolder holder = new TypeHolder();
        holder.setAge(23);
        holder.setName("Bob");

        preparedStatement.setObject(2, holder);
        preparedStatement.executeUpdate();
        preparedStatement.close();
    }
}
```

### Mapping UDT

To map the query results to the custom class, a type mapping from UDT to the custom class must also be defined (see the typeMap in the example below).

```java
package quickstart;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.HashMap;
import java.util.Map;
import java.util.Properties;

public class UDTMappingExample {

    public static Connection getConnection() throws SQLException {
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");
        Class.forName("com.yashandb.jdbc.Driver");
        return DriverManager.getConnection(url, info);
    }

    // Map UDT to custom class
    public void mapUDT(Connection conn) throws SQLException {
        try (Statement statement = conn.createStatement()) {
            ResultSet resultSet = statement.executeQuery("select * from udt_table");
            // Define the type mapping from UDT to custom class
            Map<String, Class<?>> typeMap = new HashMap<>();
            typeMap.put("USER.UDT_TYPE", TypeHolder.class);
            while (resultSet.next()) {
                // Get the custom object instance based on the type mapping
                TypeHolder udtData = (TypeHolder) resultSet.getObject(2, typeMap);
            }
            resultSet.close();
        }
    }
}
```

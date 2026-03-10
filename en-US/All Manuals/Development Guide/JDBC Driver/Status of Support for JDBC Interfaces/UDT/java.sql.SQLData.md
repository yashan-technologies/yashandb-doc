The `java.sql.SQLData` interface is used to create custom classes and map them to the UDT's Object type.

The methods included in `java.sql.SQLData` are as follows:

|Return Type |Method |Note |
|--------|--------------------------------------------|------------------------------------|
| String      | getSQLTypeName()                             | Returns the corresponding UDT type name, which must be in the schema.name full name. |
| void        | readSQL (SQLInput stream, String typeName)  | Reads each property or element of the SQL type from the provided input stream and sets the UDT type name. |
| void        | writeSQL (SQLOutput stream)                  | Writes each property of the SQL type to the provided output stream. |

>**Note**:
> 
> SQLData requires users to implement it in custom classes. Only by correctly implementing the three methods in SQLData can the mapping between UDT types and custom classes be completed.

## Creating UDT

```sql
-- The definition of UDT type and table structure is as follows:
CREATE OR replace TYPE udt_object AS object(age INT,name VARCHAR(32));
    
CREATE TABLE udt_table(id INT,person udt_object);
```

```java
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
        output.writeInt(this.id);
        output.writeString(this.member);
    }
}
```

## UDT Parameter Binding

To use UDT for parameter binding, instantiate the custom class first, then call the `setObject` method for binding.

```java
    PreparedStatement preparedStatement = conn.prepareStatement("insert into udt_table values(?,?)") ;
    preparedStatement.setInt(1,1);
    TypeHolder holder = new TypeHolder();
    holder.setAge(23);
    holder.setName("Bob");
    
    preparedStatement.setObject(2,holder);
    preparedStatement.executeUpdate();
```

## Mapping UDT

To map the query results to the custom class, a type mapping from UDT to the custom class must also be defined (see the typeMap in the example below).

Example code:
```java
    ResultSet resultSet = statement.executeQuery("select * from udt_tab");
    // Define the type mapping from UDT to custom class
    Map<String, Class<?>> typeMap = new HashMap<>();
    typeMap.put("USER.UDT_TYPE", TypeHolder.class);
    while (resultSet.next()) {
        // Get the custom object instance based on the type mapping
        TypeHolder udtData = (TypeHolder)resultSet.getObject(2, typeMap);
    }
    resultSet.close();
```

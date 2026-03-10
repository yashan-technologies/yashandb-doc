java.sql.SQLData接口用于创建自定义类并将其与UDT的Object类型进行映射。

java.sql.SQLData包含的方法如下：

| 返回类型   | 方法                                         | 备注                                 |
|--------|--------------------------------------------|------------------------------------|
| String | getSQLTypeName()                           | 返回对应的UDT类型名称，必须是schema.name全名称。    |
| void   | readSQL (SQLInput stream, String typeName) | 从给定的输入流中读取SQL类型的每个属性或元素，设置UDT类型名称。 |
| void   | writeSQL (SQLOutput stream)                | 将SQL类型的每个属性写入给定的输出流。               |

>**Note**:
> 
> SQLData需要用户在自定义类时实现，只有正确实现SQLData里面的三个方法才能完成UDT类型与自定义类的映射。
> 

## 创建UDT

```sql
--UDT类型和表结构的定义如下：
CREATE OR replace TYPE udt_object AS object(age INT,name VARCHAR(32));
    
CREATE TABLE udt_table(id INT,person udt_object);
```

```java
//自定义Java类型
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

## UDT参数绑定

如需使用自定义类型进行参数绑定，需先初始化自定义类的实例，然后调用setObject方法进行绑定。

```java
    PreparedStatement preparedStatement = conn.prepareStatement("insert into udt_table values(?,?)") ;
    preparedStatement.setInt(1,1);
    TypeHolder holder = new TypeHolder();
    holder.setAge(23);
    holder.setName("Bob");
    
    preparedStatement.setObject(2,holder);
    preparedStatement.executeUpdate();

```

## 映射UDT

如需将查询结果映射至自定义类，还需定义UDT到自定义类的类型映射（详见下述示例中的typeMap）。

写法示例：
```java
    ResultSet resultSet = statement.executeQuery("select * from udt_tab");
    // 定义UDT到自定义类的类型映射
    Map<String, Class<?>> typeMap = new HashMap<>();
    typeMap.put("USER.UDT_TYPE", TypeHolder.class);
    while (resultSet.next()) {
        // 根据类型映射获取返回的自定义对象示例
        TypeHolder udtData = (TypeHolder)resultSet.getObject(2, typeMap);
    }
    resultSet.close();
```


## 描述

`java.sql.SQLData`接口用于创建自定义类并将其与UDT的Object类型进行映射。核心作用是：让开发者自定义Java类与数据库中的自定义类型（如SQL的`STRUCT`类型、Oracle的对象类型、PostgreSQL的复合类型）之间的映射规则，实现该接口的Java类，能将数据库自定义类型的字段转换为Java对象的属性，也能将Java对象的属性反向写入数据库的自定义类型字段，是处理数据库复杂自定义类型与Java对象转换的标准方式。


详细接口说明请参考[JDBC官方网站](https://docs.oracle.com/en/java/javase/17/docs/api/java.sql/java/sql/SQLData.html)。

## 方法

java.sql.SQLData包含的方法如下：

| 方法|  返回类型| 备注|
|--------------------------------------------|--------|------------------------------------|
| getSQLTypeName()                           | String | 返回对应的UDT类型名称，必须是schema.name全名称。    |
| readSQL (SQLInput stream, String typeName) | void   | 从给定的输入流中读取SQL类型的每个属性或元素，设置UDT类型名称。 |
| writeSQL (SQLOutput stream)                | void   | 将SQL类型的每个属性写入给定的输出流。               |

>**Note**:
> 
> SQLData需要用户在自定义类时实现，只有正确实现SQLData里面的三个方法才能完成UDT类型与自定义类的映射。

## 示例

### 创建UDT

```sql
-- UDT类型和表结构的定义如下：
create or replace type udt_object as object(age int, name varchar(32));

create table udt_table(id int, person udt_object);
```

```java
package quickstart;

import java.sql.SQLData;
import java.sql.SQLInput;
import java.sql.SQLOutput;
import java.sql.SQLException;

// 自定义Java类型
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

### UDT参数绑定

如需使用自定义类型进行参数绑定，需先初始化自定义类的实例，然后调用setObject方法进行绑定。

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

    // UDT参数绑定示例
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

### 映射UDT

如需将查询结果映射至自定义类，还需定义UDT到自定义类的类型映射（详见下述示例中的typeMap）。

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

    // 映射UDT到自定义类
    public void mapUDT(Connection conn) throws SQLException {
        try (Statement statement = conn.createStatement()) {
            ResultSet resultSet = statement.executeQuery("select * from udt_table");
            // 定义UDT到自定义类的类型映射
            Map<String, Class<?>> typeMap = new HashMap<>();
            typeMap.put("USER.UDT_TYPE", TypeHolder.class);
            while (resultSet.next()) {
                // 根据类型映射获取返回的自定义对象示例
                TypeHolder udtData = (TypeHolder) resultSet.getObject(2, typeMap);
            }
            resultSet.close();
        }
    }
}
```

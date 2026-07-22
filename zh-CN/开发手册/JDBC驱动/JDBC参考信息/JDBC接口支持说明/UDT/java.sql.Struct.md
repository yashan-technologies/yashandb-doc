## 描述

Struct用来表示数据库中的UDT Object类型的数据。

`java.sql.Struct`是JDBC规范中用于映射数据库结构化类型（SQL STRUCT类型）的核心接口，它是数据库结构化类型在Java程序中的直接映射对象，数据库中的`STRUCT`类型（本质是一组命名字段的集合，比如包含`id`、`name`、`age`的结构化数据），通过JDBC查询后会被封装为`Struct`实例，开发者可通过该接口的方法获取结构化类型的名称、字段值数组，实现对数据库结构化数据的读取和操作。


详细接口说明请参考[JDBC官方网站](https://docs.oracle.com/en/java/javase/17/docs/api/java.sql/java/sql/Struct.html)。

## 方法

`java.sql.Struct`包含的方法如下：

| 方法|  返回类型| 备注|
|------------------------------------------|----------|------------------------------------|
| getSQLTypeName()                         | String   | 返回UDT类型全称。                         |
| getAttributes()                          | Object[] | 返回UDT类型数据的Object[]对象。              |
| getAttributes(Map<String, Class<?>> map) | Object[] | 添加UDT映射SQLData对象集合，返回对应的SQLData对象。 |
| setAttributes(Object[] attributes)       | void     | 设置UDT类型数据。                         |

## 示例

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

    // 读取 Struct 数据
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

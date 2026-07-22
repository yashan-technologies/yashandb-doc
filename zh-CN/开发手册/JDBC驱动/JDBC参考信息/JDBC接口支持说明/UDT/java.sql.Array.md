## 描述

Array用来表示数据库中的Array和Nested Table类型的数据。

`java.sql.Array`是JDBC规范中用于映射数据库数组类型的核心接口，核心作用是：将数据库中定义的数组类型字段读取为Java程序可操作的对象，也可将Java中的数组写入到数据库的数组类型字段中。


详细接口说明请参考[JDBC官方网站](https://docs.oracle.com/en/java/javase/17/docs/api/java.sql/java/sql/Array.html)。

## 方法

java.sql.Array包含以下方法：

| 方法|  返回类型| 备注|
|----------------------------------------------------------------|-----------|------------------------------------------------------------------------------|
| isFreed()                                                      | boolean   | 返回Array对象是否已经释放。                                                             |
| free()                                                         | void      | 释放Array对象。                                                                   |
| setElementTypeCode(int elementTypeCode)                        | void      | 设置Array数据类型的类型码，并设置对应的类型名称。                                                  |
| setElements(Object[] elements)                                 | void      | 设置Array数据。                                                                   |
| setElementTypeName(String elementTypeName)                     | void      | 设置数据类型名称。                                                                    |
| getBaseTypeName()                                              | String    | 返回Array数据类型名称。                                                               |
| getBaseType()                                                  | int       | 返回Array数据类型码。                                                                |
| getArray()                                                     | Object    | 返回Array数据类型。                                                                 |
| getArray(Map<String, Class<?>> map)                            | Object    | 添加UDT类型映射集合，对Array数据进行解析映射，返回对应的数据对象。                                        |
| getArray(long index, int count)                                | Object    | 返回 `[index, index + count)` 范围的数据。                                           |
| getArray(long index, int count, Map<String, Class<?>> map)     | Object    | 添加UDT类型映射集合，对Array数据进行解析映射，返回 `[index, index + count)` 范围的数据。                |
| getResultSet()                                                 | ResultSet | 返回Array数据的ResultSet结果集，ResultSet结果集有两列，第一列为数据在原Array中的index，从1开始，第二列为真实数据。   |
| getResultSet(Map<String, Class<?>> map)                        | ResultSet | 添加UDT类型映射集合，返回Array数据的ResultSet结果集，ResultSet列信息同上。                           |
| getResultSet(long index, int count)                            | ResultSet | 返回 `[index, index + count)` 范围的数据对应的ResultSet结果集，ResultSet列信息同上。             |
| getResultSet(long index, int count, Map<String, Class<?>> map) | ResultSet | 添加UDT类型映射集合，返回 `[index, index + count)` 范围的数据对应的ResultSet结果集，ResultSet列信息同上。 |

## 示例

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

    // 读取Array数据
    public static void readArrayData(Connection conn) throws SQLException {
        // 先创建测试用的表和数据
        conn.setAutoCommit(false); // 关闭自动提交
            // 创建数组类型（检查是否存在）
            try {
                // 先尝试删除（如果有关联表需要先删表）
                stmt.execute("DROP TYPE IF EXISTS int_array");
                stmt.execute("CREATE TYPE int_array AS VARRAY(10) OF INT");
            } catch (SQLException e) {
                // 尝试直接创建（如果已存在）
                    stmt.execute("CREATE TYPE int_array AS VARRAY(10) OF INT");
                } catch (SQLException e2) {
                    System.out.println("Type info: " + e2.getMessage());
                }
            }

            // 创建表（使用 IF NOT EXISTS）
            try {
                stmt.execute("CREATE TABLE IF NOT EXISTS udt_tab (id INT, arr int_array)");
            } catch (SQLException e) {
                System.out.println("Table info: " + e.getMessage());
            }

            // 插入测试数据（先删除再插入，处理唯一约束冲突）
            try {
                stmt.execute("DELETE FROM udt_tab"); // 清空表后重新插入
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
            conn.setAutoCommit(true); // 恢复自动提交

        // 读取Array数据
        try (Statement stmt = conn.createStatement();
             ResultSet resultSet = stmt.executeQuery("select * from udt_tab")) {

            while (resultSet.next()) {
                int id = resultSet.getInt(1);
                Array array = resultSet.getArray(2);
                if (array != null) {
                    String typeName = array.getBaseTypeName();
                    int typeCode = array.getBaseType();
                    // 获取UDT Array的数据
                    Object[] element = (Object[]) array.getArray();
                    // 把UDT Array的数据封装成ResultSet的形式供后续读取
                    try (ResultSet rs = array.getResultSet()) {
                        System.out.println("ID: " + id + ", Array elements:");
                        while (rs.next()) {
                            // 处理Array元素
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

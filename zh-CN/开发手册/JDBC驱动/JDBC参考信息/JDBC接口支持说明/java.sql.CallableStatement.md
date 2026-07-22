## 描述

CallableStatement接口用于实现PL对象的调用。

`java.sql.CallableStatement`是JDBC规范中专门用于调用数据库存储过程和函数的接口，是Java程序与数据库存储过程交互的专用通道。


本接口不适用于存算一体分布式集群部署。

详细接口说明请参考[JDBC官方网站](https://docs.oracle.com/en/java/javase/17/docs/api/java.sql/java/sql/CallableStatement.html)。

## 方法

YashanDB JDBC驱动对CallableStatement接口有如下方法：

| 方法|  返回类型|
|-------------------------------------------------------|---------------------|
| registerOutParameter(int parameterIndex, int sqlType) | void                |
| registerOutParameter(int parameterIndex, int sqlType, int scale) | void |
| registerOutParameter(String parameterName, int sqlType) | void |
| registerOutParameter(String parameterName, int sqlType, int scale) | void |
| registerOutParameter(int parameterIndex, SQLType sqlType) | void | 
| registerOutParameter(int parameterIndex, SQLType sqlType, int scale) | void | 
| registerOutParameter(String parameterName, SQLType sqlType) | void | 
| registerOutParameter(String parameterName, SQLType sqlType, int scale) | void | 
| registerOutParameter(int parameterIndex, SQLType sqlType, String typeName) | void | 
| registerOutParameter(String parameterName, SQLType sqlType, String typeName) | void | 
| registerOutParameter(String parameterName, int sqlType, String typeName) | void | 
| registerOutParameter(int parameterIndex, int sqlType, String typeName) | void | 
| setNull(String parameterName, int sqlType) | void |
| setNull(String parameterName, int sqlType, String typeName) | void |
| setBoolean(String parameterName, boolean x) | void |
| setByte(String parameterName, byte x) | void |
| setShort(String parameterName, short x) | void |
| setInt(String parameterName, int x) | void |
| setLong(String parameterName, long x) | void |
| setFloat(String parameterName, float x) | void |
| setDouble(String parameterName, double x) | void |
| setBigDecimal(String parameterName, BigDecimal x) | void |
| setString(String parameterName, String x) | void |
| setBytes(String parameterName, byte x\[\]) | void |
| setDate(String parameterName, java.sql.Date x) | void |
| setTime(String parameterName, java.sql.Time x) | void |
| setTimestamp(String parameterName, java.sql.Timestamp x) | void |
| setAsciiStream(String parameterName, java.io.InputStream x, int length) | void |
| setBinaryStream(String parameterName, java.io.InputStream x, int length) | void |
| setObject(String parameterName, Object x, int targetSqlType, int scale) | void |
| setObject(String parameterName, Object x, int targetSqlType) | void |
| setObject(String parameterName, Object x) | void |
| setCharacterStream(String parameterName,java.io.Reader reader, int length) | void |
| setDate(String parameterName, java.sql.Date x, Calendar cal) | void |
| setTime(String parameterName, java.sql.Time x, Calendar cal) | void |
| setTimestamp(String parameterName, java.sql.Timestamp x, Calendar cal) | void |
| setNString(String parameterName, String value) | void |
| setNCharacterStream(String parameterName, Reader value, long length) | void |
| setNClob(String parameterName, NClob value) | void |
| setClob(String parameterName, Reader reader, long length) | void |
| setBlob(String parameterName, InputStream inputStream, long length) | void |
| setNClob(String parameterName, Reader reader, long length) | void |
| setSQLXML(String parameterName, SQLXML xmlObject) | void |
| setBlob (String parameterName, Blob x) | void |
| setClob (String parameterName, Clob x) | void |
| setAsciiStream(String parameterName, java.io.InputStream x, long length) | void |
| setCharacterStream(String parameterName,java.io.Reader reader,long length) | void |
| setBinaryStream(String parameterName, java.io.InputStream x, long length) | void |
| setAsciiStream(String parameterName, java.io.InputStream x) | void |
| setBinaryStream(String parameterName, java.io.InputStream x) | void |
| setCharacterStream(String parameterName,java.io.Reader reader) | void |
| setNCharacterStream(String parameterName, Reader value) | void |
| setClob(String parameterName, Reader reader) | void |
| setBlob(String parameterName, InputStream inputStream) | void |
| setNClob(String parameterName, Reader reader) | void |
| getString(String parameterName) | String |
| getString(int parameterIndex)                         | String              |
| getNString(int parameterIndex) | String |
| getNString(String parameterName) | String |
| getBoolean(int parameterIndex)                        | boolean             |
| getByte(int parameterIndex)                           | byte                |
| getShort(int parameterIndex)                          | short               |
| getShort(String parameterName) | short |
| getInt(int parameterIndex)                            | int                 |
| getInt(String parameterName) | int |
| getLong(int parameterIndex)                           | long                |
| getLong(String parameterName) | long |
| getFloat(int parameterIndex)                          | float               |
| getFloat(String parameterName) | float |
| getDouble(int parameterIndex)                         | double              |
| getDouble(String parameterName) | double |
| getBytes(int parameterIndex)                          | byte\[\]            |
| getBytes(String parameterName) | byte\[\] |
| getByte(String parameterName) | byte |
| getDate(int parameterIndex)                           | java.sql.Date       |
| getDate(String parameterName) | java.sql.Date |
| getTime(int parameterIndex)                           | java.sql.Time       |
| getTime(String parameterName) | java.sql.Time |
| getTimestamp(int parameterIndex)                      | java.sql.Timestamp  |
| getTimestamp(String parameterName) | java.sql.Timestamp |
| getObject(int parameterIndex)                         | Object              |
| getObject(int parameterIndex, java.util.Map<String,Class<?>> map) | Object |
| getObject(String parameterName, java.util.Map<String,Class<?>> map) | Object |
| getObject(String parameterName) | Object |
| getBigDecimal(int parameterIndex)                     | BigDecimal          |
| getBigDecimal(String parameterName) | BigDecimal |
| getBigDecimal(int  parameterIndex, int  scale) | BigDecimal |
| getDate(int parameterIndex, Calendar cal)             | java.sql.Date       |
| getDate(String parameterName, Calendar cal) | java.sql.Date |
| getTime(int parameterIndex, Calendar cal)             | java.sql.Time       |
| getTime(String parameterName, Calendar cal) | java.sql.Time |
| getTimestamp(int parameterIndex, Calendar cal)        | java.sql.Timestamp  |
| getTimestamp(String parameterName, Calendar cal) | java.sql.Timestamp |
| getURL(String parameterName) | java.net.URL |
| getNCharacterStream(int parameterIndex) | java.io.Reader |
| getNCharacterStream(String parameterName) | java.io.Reader |
| getCharacterStream(int parameterIndex) | java.io.Reader |
| getCharacterStream(String parameterName) | java.io.Reader |
| getRowId(int parameterIndex)                          | RowId               |
| getRowId(String parameterName)                        | RowId               |
| setRowId(String parameterName, RowId x)               | void                |
| getClob (int parameterIndex)                          | Clob                |
| getClob (String parameterName) | Clob |
| getBlob (int parameterIndex)                          | Blob                |
| getBlob (String parameterName) | Blob |
| getGeneratedKeys()                                    | ResultSet           |
| wasNull()                                             | boolean             |
| getBoolean(String parameterName) | boolean |
| getSQLXML(int parameterIndex)                         | SQLXML              |
| getSQLXML(String parameterName)                       | SQLXML              |
| getArray (int parameterIndex) | Array |
| getArray (String parameterName) | Array |
| getNClob (int parameterIndex) | NClob |
| getNClob (String parameterName) | NClob |
| getObject(int parameterIndex, Class<T> type) | public <T> T |
| getObject(String parameterName, Class<T> type) | public <T> T |

## 示例

```java
package quickstart;

import java.sql.CallableStatement;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.sql.Types;
import java.util.Properties;

public class CallableStatementExample {

    public static Connection getConnection() throws SQLException {
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");
        Class.forName("com.yashandb.jdbc.Driver");
        return DriverManager.getConnection(url, info);
    }

    // 完整存储过程调用示例
    public static String getUserInfo(Connection conn, int userId) throws SQLException {
        String sql = "{call GET_USER_INFO(?, ?, ?, ?, ?)}";
        try (CallableStatement cs = conn.prepareCall(sql)) {
            // 设置输入参数
            cs.setInt(1, userId);

            // 注册输出参数 (每个参数只注册一次)
            cs.registerOutParameter(2, Types.VARCHAR);  // 用户名
            cs.registerOutParameter(3, Types.VARCHAR);  // 邮箱
            cs.registerOutParameter(4, Types.INTEGER);  // 年龄
            cs.registerOutParameter(5, Types.DATE);     // 注册日期

            cs.execute();

            // 获取结果
            String name = cs.getString(2);
            String email = cs.getString(3);
            int age = cs.getInt(4);
            java.sql.Date registerDate = cs.getDate(5);

            return name + ", " + email + ", " + age + ", " + registerDate;
        }
    }

    public static void main(String[] args) {
        try (Connection conn = getConnection()) {
            String result = getUserInfo(conn, 1);
            System.out.println("Result: " + result);
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
```


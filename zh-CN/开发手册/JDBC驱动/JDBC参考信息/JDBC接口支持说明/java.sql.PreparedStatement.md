## 描述

`java.sql.PreparedStatement`接口继承了Statement对象，是实现JDBC预编译SQL语句的对象。


用户可将SQL语句预编译并存储在PreparedStatement对象中，之后可以使用此对象多次有效地执行SQL语句。

详细接口说明请参考[JDBC官方网站](https://docs.oracle.com/en/java/javase/17/docs/api/java.sql/java/sql/PreparedStatement.html)。

> **Note**:
>
> YashanDB中，PreparedStatement的SQL语句最多支持32000个参数。

## 方法

YashanDB JDBC驱动对PreparedStatement接口有如下方法：

| 方法|  返回类型|
| --- | --- |
| addBatch() | void |
| clearParameters() | void |
| setArray​(int parameterIndex, Array x) | void |
| execute() | boolean |
| executeLargeUpdate() | long |
| executeQuery() | ResultSet |
| executeUpdate() | int |
| getGeneratedKeys() | ResultSet |
| getMetaData() | ResultSetMetaData |
| getParameterMetaData() | ParameterMetaData |
| setBigDecimal​(int parameterIndex, BigDecimal x) | void |
| setBoolean​(int parameterIndex, boolean x) | void |
| setByte​(int parameterIndex, byte x) | void |
| setDate​(int parameterIndex, Date x) | void |
| setDate​(int parameterIndex, Date x, Calendar cal) | void |
| setDouble​(int parameterIndex, double x) | void |
| setFloat​(int parameterIndex, float x) | void |
| setInt​(int parameterIndex, int x) | void |
| setLong​(int parameterIndex, long x) | void |
| setBlob​(int parameterIndex, Blob x) | void |
| setBytes​(int parameterIndex, byte\[\] x) | void |
| setClob​(int parameterIndex, Clob x) | void |
| setNull​(int parameterIndex, int sqlType) | void |
| setNull(int index, int sqlType, String typeName) | void |
| setObject​(int parameterIndex, Object x) | void |
| setObject​(int parameterIndex, Object x, int targetSqlType) | void |
| setObject​(int parameterIndex, Object x, int targetSqlType, int scaleOrLength) | void |
| setObject​(int parameterIndex, Object x, SQLType targetSqlType) | void |
| setObject​(int parameterIndex, Object x, SQLType targetSqlType, int scaleOrLength) | void |
| setRowId​(int parameterIndex, RowId x) | void |
| setShort​(int parameterIndex, short x) | void |
| setString​(int parameterIndex, String x) | void |
| setNString​(int parameterIndex, String value) | void |
| setTimestamp​(int parameterIndex, Timestamp x) | void |
| setTimestamp​(int parameterIndex, Timestamp x, Calendar cal) | void |
| setTimestamp(int i, java.time.OffsetDateTime offsetDateTime) | void |
| setTime​(int parameterIndex, Time x) | void |
| setTime​(int parameterIndex, Time x, Calendar cal) | void |
| setBinaryStream​(int parameterIndex, InputStream x) | void |
| setBinaryStream​(int parameterIndex, InputStream x, int length) | void |
| setBinaryStream​(int parameterIndex, InputStream x, long length) | void |
| setBlob​(int parameterIndex, InputStream inputStream) | void |
| setBlob​(int parameterIndex, InputStream inputStream, long length) | void |
| setClob​(int parameterIndex, Reader reader) | void |
| setClob​(int parameterIndex, Reader reader, long length) | void |
| setNClob​(int parameterIndex, Reader reader) | void | 
| setNClob​(int parameterIndex, Reader reader, long length) | void | 
| setNClob​(int parameterIndex, NClob value) | void | 
| setAsciiStream​(int parameterIndex, InputStream x) | void |
| setAsciiStream​(int parameterIndex, InputStream x, int length) | void |
| setAsciiStream​(int parameterIndex, InputStream x, long length) | void |
| setCharacterStream​(int parameterIndex, Reader reader) | void |
| setCharacterStream​(int parameterIndex, Reader reader, int length) | void |
| setCharacterStream​(int parameterIndex, Reader reader, long length) | void |
| setSQLXML​(int parameterIndex, SQLXML xmlObject) | void |

YashanDB JDBC驱动PreparedStatement扩展接口功能：

|  方法| 说明|
| ------------------------------------------- |-----------------------|
| void setIsBatchError(boolean batchError); | 设置是否批量报错，若设置为true，则多条数据为一批进行批量执行时，如果某一条或者某几条有数据错误时不会立即报错停止执行，而是把后面的全部执行完，最后通过getBatchError来获取报错行的报错信息。 |
| boolean getIsBatchError(); | 获取是否批量报错。   |
| String getBatchError(int batchIndex); | 在setIsBatchError设置为true的情况下在批量执行结束后获取报错行的报错信息。     |

## 示例

```java
package quickstart;

import java.math.BigDecimal;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.Properties;

public class PreparedStatementExample {

    public static Connection getConnection() throws SQLException {
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");
        Class.forName("com.yashandb.jdbc.Driver");
        return DriverManager.getConnection(url, info);
    }

    // 分页查询
    public void pagedQuery(Connection conn, int page, int size) throws SQLException {
        String sql = "SELECT * FROM products ORDER BY id LIMIT ? OFFSET ?";
        try (PreparedStatement ps = conn.prepareStatement(sql)) {
            ps.setInt(1, size);
            ps.setInt(2, (page - 1) * size);
            try (ResultSet rs = ps.executeQuery()) {
                // Process results...
            }
        }
    }

    // 条件查询
    public void conditionalQuery(Connection conn, String name, Integer minAge)
            throws SQLException {
        String sql = "SELECT * FROM users WHERE 1=1";
        if (name != null) sql += " AND name LIKE ?";
        if (minAge != null) sql += " AND age >= ?";

        try (PreparedStatement ps = conn.prepareStatement(sql)) {
            int index = 1;
            if (name != null) ps.setString(index++, "%" + name + "%");
            if (minAge != null) ps.setInt(index++, minAge);

            ps.executeQuery();
        }
    }

    // 事务更新
    public void transactionUpdate(Connection conn) throws SQLException {
        conn.setAutoCommit(false);
        String sql = "UPDATE accounts SET balance = balance - ? WHERE id = ?";

        try (PreparedStatement ps = conn.prepareStatement(sql)) {
            // 从账户1转出资金
            ps.setBigDecimal(1, new BigDecimal("100"));
            ps.setInt(2, 1);
            ps.executeUpdate();

            // 向账户2转入资金
            ps.setBigDecimal(1, new BigDecimal("100"));
            ps.setInt(2, 2);
            ps.executeUpdate();

            conn.commit();
        } catch (SQLException e) {
            conn.rollback();
            throw e;
        } finally {
            conn.setAutoCommit(true);
        }
    }
}
```


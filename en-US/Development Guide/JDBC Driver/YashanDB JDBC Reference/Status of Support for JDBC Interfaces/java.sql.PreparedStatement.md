## Description

`java.sql.PreparedStatement` represents that it extends the Statement object and is used to implement JDBC precompiled SQL statements.


Users can precompile an SQL statement and store it in a PreparedStatement object, which can then be used to efficiently execute the SQL statement multiple times.

Detailed interface specifications, please refer to [The JDBC Official Website](https://docs.oracle.com/en/java/javase/17/docs/api/java.sql/java/sql/PreparedStatement.html).

> **Note**:
>
> In YashanDB, the SQL statement in PreparedStatement supports up to 32,000 parameters at most.

## Method

The YashanDB JDBC driver has the following methods for the PreparedStatement interface:

|Method |Return Type |
| --- | --- |
| addBatch() | void |
| clearParameters() | void |
| setArray​(int parameterIndex, Array x) | void |
| execute() | boolean |
| executeLargeUpdate() | long |
| executeQuery() | ResultSet |
| executeUpdate() | int |
| getGeneratedKeys() | ResultSet |
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
| setAsciiStream​(int parameterIndex, InputStream x) | void |
| setAsciiStream​(int parameterIndex, InputStream x, int length) | void |
| setAsciiStream​(int parameterIndex, InputStream x, long length) | void |
| setCharacterStream​(int parameterIndex, Reader reader) | void |
| setCharacterStream​(int parameterIndex, Reader reader, int length) | void |
| setCharacterStream​(int parameterIndex, Reader reader, long length) | void |
| setSQLXML​(int parameterIndex, SQLXML xmlObject) | void |

The YashanDB JDBC driver PreparedStatement extension interface functionality:

|Method |Description |
| ------------------------------------------- |-----------------------|
| void setIsBatchError(boolean batchError);    | Set whether to batch errors. If set to true, when executing multiple rows as a batch, if any row has data errors, execution will not stop immediately, but will complete all subsequent executions, and finally obtain error information of the erroneous rows through getBatchError. |
| boolean getIsBatchError();                    | Get whether to batch errors.   |
| String getBatchError(int batchIndex);        | Under the condition that setIsBatchError is set to true, obtain the error information of the erroneous rows after the batch execution ends.     |

## Example

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

    // Paginated query
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

    // Conditional query
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

    // Transaction update
    public void transactionUpdate(Connection conn) throws SQLException {
        conn.setAutoCommit(false);
        String sql = "UPDATE accounts SET balance = balance - ? WHERE id = ?";

        try (PreparedStatement ps = conn.prepareStatement(sql)) {
            // Transfer out from account 1
            ps.setBigDecimal(1, new BigDecimal("100"));
            ps.setInt(2, 1);
            ps.executeUpdate();

            // Transfer into account 2
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


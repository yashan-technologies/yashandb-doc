## Description

`java.sql.ResultSet` represents that it represents the result set generated after the execution of an SQL query statement. It can be obtained via the `executeQuery()` method or `getResultSet()` method of the `Statement` interface. Its core functions are as follows:


- Stores all row data returned by a database query;
- Provides methods to iterate over, read, and manipulate row/column data;
- Supports flexible positioning within the result set (e.g., moving to a specified row, bidirectional traversal);
- Enables updating the result set and synchronizing the changes to the database in certain scenarios.

Detailed interface specifications, please refer to [The JDBC Official Website](https://docs.oracle.com/en/java/javase/17/docs/api/java.sql/java/sql/ResultSet.html).

## Method

The YashanDB JDBC driver has the following methods for the ResultSet interface:

|Method |Return Type |
|--------------------------------------------------------------------------------------|---------------------|
| next()                                                                               | boolean             |
| close()                                                                              | void                |
| wasNull()                                                                            | boolean             |
| getString(int columnIndex)                                                           | String              |
| getString(String columnLabel)                                                        | String              |
| getNString(int columnIndex)                                                          | String              |
| getNString(String columnLabel)                                                       | String              |
| getBoolean(int columnIndex)                                                          | boolean             |
| getBoolean(String columnLabel)                                                       | boolean             |
| getByte(int columnIndex)                                                             | byte                |
| getByte(String columnLabel)                                                          | byte                |
| getShort(int columnIndex)                                                            | short               |
| getShort(String columnLabel)                                                         | short               |
| getInt(int columnIndex)                                                              | int                 |
| getInt(String columnLabel)                                                           | int                 |
| getLong(int columnIndex)                                                             | long                |
| getLong(String columnLabel)                                                          | long                |
| getNClob(int columnIndex)                                                            | NClob               |
| getNClob(String columnLabel)                                                         | NClob               |
| getSQLXML(int columnIndex)                                                           | SQLXML              |
| getSQLXML(String columnLabel)                                                        | SQLXML              |
| getFloat(int columnIndex)                                                            | float               |
| getFloat(String columnLabel)                                                         | float               |
| getDouble(int columnIndex)                                                           | double              |
| getDouble(String columnLabel)                                                        | double              |
| getBigDecimal(int columnIndex)                                                       | BigDecimal          |
| getBigDecimal(String columnLabel)                                                    | BigDecimal          |
| getBigDecimal(int columnIndex, int scale)                                            | BigDecimal          |
| getBigDecimal(String columnLabel, int scale)                                         | BigDecimal          |
| getBytes(int columnIndex)                                                            | byte\[\]            |
| getBytes(String columnLabel)                                                         | byte\[\]            |
| getDate(int columnIndex)                                                             | java.sql.Date       |
| getDate(String columnLabel)                                                          | java.sql.Date       |
| getDate(int columnIndex, Calendar cal)                                               | java.sql.Date       |
| getDate(String columnLabel, Calendar cal)                                            | java.sql.Date       |
| getTimestamp(int columnIndex)                                                        | java.sql.Timestamp  |
| getTimestamp(String columnLabel)                                                     | java.sql.Timestamp  |
| getTimestamp(int columnIndex, Calendar cal)                                          | java.sql.Timestamp  |
| getTimestamp(String columnLabel, Calendar cal)                                       | java.sql.Timestamp  |
| getAsciiStream(int columnIndex)                                                      | java.io.InputStream |
| getAsciiStream(String columnLabel)                                                   | java.io.InputStream |
| getBinaryStream(int columnIndex)                                                     | java.io.InputStream |
| getBinaryStream(String columnLabel)                                                  | java.io.InputStream |
| getCharacterStream(int columnIndex)                                                  | java.io.Reader      |
| getCharacterStream(String columnLabel)                                               | java.io.Reader      |
| getMetaData()                                                                        | ResultSetMetaData   |
| getObject(int columnIndex)                                                           | Object              |
| getObject(String columnLabel)                                                        | Object              |
| findColumn(String columnLabel)                                                       | int                 |
| isFirst()                                                                            | boolean             |
| getRow()                                                                             | int                 |
| getType()                                                                            | int                 |
| getConcurrency()                                                                     | int                 |
| getFetchDirection()                                                                  | int                 | 
| getFetchSize()                                                                       | int                 | 
| getWarnings()                                                                        | int                 | 
| getStatement()                                                                       | Statement           |
| getRowId(int columnIndex)                                                            | RowId               |
| getRowId(String columnLabel)                                                         | RowId               |
| isClosed()                                                                           | boolean             |
| getNCharacterStream(int columnIndex)                                                 | java.io.Reader      |
| getNCharacterStream(String columnLabel)                                              | java.io.Reader      |
| getTime(int columnIndex)                                                             | java.sql.Time       |
| getTime(String columnLabel)                                                          | java.sql.Time       |
| getTime(int columnIndex, Calendar cal)                                               | java.sql.Time       |
| getTime(String columnLabel, Calendar cal)                                            | java.sql.Time       |
| getBlob(int columnIndex)                                                             | Blob                |
| getBlob(String columnLabel)                                                          | Blob                |
| getClob(int columnIndex)                                                             | Clob                |
| getClob(String columnLabel)                                                          | Clob                |
| getObject(int columnIndex, Class<T> type)                                            | <T> T               |
| getObject(String columnLabel, Class<T> type)                                         | <T> T               |
| getObject(int columnIndex, java.util.Map<String,Class<?>> map)                       | Object              |
| getObject(String columnLabel, java.util.Map<String,Class<?>> map)                    | Object              |
| getArray(int columnIndex)                                                            | Array               |
| getArray(String columnLabel)                                                         | Array               |
| isBeforeFirst()                                                                      | boolean             |
| isAfterLast()                                                                        | boolean             |
| isLast()                                                                             | boolean             |
| beforeFirst()                                                                        | void                |
| afterLast()                                                                          | void                |
| first()                                                                              | boolean             |
| last()                                                                               | boolean             |
| absolute( int row )                                                                  | boolean             |
| relative( int rows )                                                                 | boolean             |
| previous()                                                                           | boolean             |
| rowUpdated()                                                                         | boolean             |
| rowInserted()                                                                        | boolean             |
| rowDeleted()                                                                         | boolean             |
| clearWarnings()                                                                      | void                |
| setFetchSize(int rows)                                                               | void                |
| updateNull(int columnIndex)                                                          | void                |
| updateNull(String columnLabel)                                                       | void                |
| updateByte(int columnIndex, byte x)                                                  | void                |
| updateByte(String columnLabel, byte x)                                               | void                |
| updateShort(int columnIndex, short x)                                                | void                |
| updateShort(String columnLabel, short x)                                             | void                |
| updateInt(int columnIndex, int x)                                                    | void                |
| updateInt(String columnLabel, int x)                                                 | void                |
| updateLong(int columnIndex, long x)                                                  | void                |
| updateLong(String columnLabel, long x)                                               | void                |
| updateFloat(int columnIndex, float x)                                                | void                |
| updateFloat(String columnLabel, float x)                                             | void                |
| updateDouble(int columnIndex, double x)                                              | void                |
| updateDouble(String columnLabel, double x)                                           | void                |
| updateBigDecimal(int columnIndex, BigDecimal x)                                      | void                |
| updateBigDecimal(String columnLabel, BigDecimal x)                                   | void                |
| updateString(int columnIndex, String x)                                              | void                |
| updateString(String columnLabel, String x)                                           | void                |
| updateSQLXML(int columnIndex, SQLXML xmlObject)                                      | void                |
| updateSQLXML(String columnLabel, SQLXML xmlObject)                                   | void                |
| updateDate(int columnIndex, java.sql.Date x)                                         | void                |
| updateDate(String columnLabel, java.sql.Date x)                                      | void                |
| updateTime(int columnIndex, java.sql.Time x)                                         | void                |
| updateTime(String columnLabel, java.sql.Time x)                                      | void                |
| updateTimestamp(int columnIndex, java.sql.Timestamp x)                               | void                |
| updateTimestamp(String columnLabel, java.sql.Timestamp x)                            | void                |
| updateBoolean(String columnLabel, boolean x)                                         | void                |
| updateObject(int columnIndex, Object x, int scaleOrLength)                           | void                |
| updateObject(int columnIndex, Object x)                                              | void                |
| updateObject(String columnLabel, Object x, int scaleOrLength)                        | void                |
| updateObject(String columnLabel, Object x)                                           | void                |
| updateObject(int columnIndex, Object x, SQLType targetSqlType, int scaleOrLength)    | void                |
| updateObject(String columnLabel, Object x, SQLType targetSqlType, int scaleOrLength) | void                |
| updateObject(int columnIndex, Object x, SQLType targetSqlType)                       | void                |
| updateObject(String columnLabel, Object x, SQLType targetSqlType)                    | void                |
| insertRow()                                                                          | void                |
| updateRow()                                                                          | void                |
| deleteRow()                                                                          | void                |
| updateBytes(int columnIndex, byte x\[\])                                             | void                |
| updateBytes(String columnLabel, byte x\[\])                                          | void                |
| updateRowId(int columnIndex, RowId x)                                                | void                |
| updateRowId(String columnLabel, RowId x)                                             | void                |
| moveToInsertRow()                                                                    | void                |
| moveToCurrentRow()                                                                   | void                |
| refreshRow()                                                                         | void                |
| setFetchDirection(int direction)                                                     | void                | 

## Example

```java
package quickstart;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.ArrayList;
import java.util.List;
import java.util.Properties;

public class ResultSetExample {

    public static Connection getConnection() throws SQLException {
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");
        Class.forName("com.yashandb.jdbc.Driver");
        return DriverManager.getConnection(url, info);
    }

    // Read single row
    public String getUserById(Connection conn, int id) throws SQLException {
        String sql = "SELECT id, name, email FROM users WHERE id = ?";
        try (PreparedStatement ps = conn.prepareStatement(sql)) {
            ps.setInt(1, id);
            try (ResultSet rs = ps.executeQuery()) {
                if (rs.next()) {
                    return rs.getInt("id") + ", " + rs.getString("name") + ", " + rs.getString("email");
                }
            }
        }
        return null;
    }

    // Read multiple rows
    public List<String> getAllUsers(Connection conn) throws SQLException {
        List<String> users = new ArrayList<>();
        String sql = "SELECT id, name FROM users ORDER BY id";

        try (Statement stmt = conn.createStatement();
             ResultSet rs = stmt.executeQuery(sql)) {
            while (rs.next()) {
                users.add(rs.getInt("id") + ", " + rs.getString("name"));
            }
        }
        return users;
    }

    // Updatable ResultSet
    public void updateUserAge(Connection conn) throws SQLException {
        String sql = "SELECT id, name, age FROM users FOR UPDATE";
        try (Statement stmt = conn.createStatement(
                ResultSet.TYPE_SCROLL_SENSITIVE,
                ResultSet.CONCUR_UPDATABLE);
             ResultSet rs = stmt.executeQuery(sql)) {

            while (rs.next()) {
                int age = rs.getInt("age");
                rs.updateInt("age", age + 1);
                rs.updateRow();
            }
        }
    }

    // Batch reading
    public void processLargeResultSet(ResultSet rs) throws SQLException {
        int batchSize = 100;
        int count = 0;

        while (rs.next()) {
            processRow(rs);
            count++;

            if (count % batchSize == 0) {
                System.out.println("Processed " + count + " rows");
            }
        }
    }

    private void processRow(ResultSet rs) throws SQLException {
        // Process each row
    }
}
```


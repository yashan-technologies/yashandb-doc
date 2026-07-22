### ResultSet Object

java.sql.ResultSet is an interface provided by JDBC for storing and manipulating the results of database queries. It is returned by the executeQuery method of Statement/PreparedStatement, and is essentially a cursor-based dataset. You can think of it as a pointer (cursor) to the query results, which by default can only move forward and read data row by row.

Example of creating a ResultSet object:

   ```java
   //Create a Statement object.
   stmt = conn.createStatement();

   //Create customer table
   stmt.execute("CREATE TABLE IF NOT EXISTS customer(id INTEGER, name VARCHAR(32))");

   //Create a ResultSet object.
   ResultSet rs = stmt.executeQuery("SELECT * FROM customer");
   ```

### Set Result Set Type

Different types of result sets have their respective application scenarios, and applications need to select the appropriate result set type based on actual circumstances. In the process of executing SQL statements, it is required to first create the corresponding statement object. Some methods for creating statement objects provide functionality to set the result set type. The relevant Connection methods are as follows:

The result set type descriptions are as follows:

|Parameter   |Description                                         |
| -------------------- | ------------------------------------------------------------ |
| resultSetType        | Indicates the type of the result set. Specifically, there are three types:<br>\*   ResultSet.TYPE_FORWARD_ONLY: ResultSet can only move forward, is the default value.<br>\*   ResultSet.TYPE_SCROLL_SENSITIVE: Can scroll back to the modified row after modification to see the modified result.<br>\*   ResultSet.TYPE_SCROLL_INSENSITIVE: Edits made to editable routines are not displayed. |
| resultSetConcurrency | Indicates the concurrency of the result set, specifically two types:<br>\*   ResultSet.CONCUR_READ_ONLY: Cannot update data in the result set unless establishing a new update statement from data in the result set.<br>\*   ResultSet.CONCUR_UPDATEABLE: Editable result set. For scrollable result sets, appropriate changes can be made to the result set. |

The visibility of different ResultSet types to data changes is as follows:

|ResultSet Type |Visible Internal Delete? |Visible Internal Update? |Visible Internal Insert? |Visible External Delete? |Visible External Update? |Visible External Insert? |
|---|---|---|---|---|---|---|
| forward-only | No | Yes | No | No | No | No |
| scroll-sensitive | Yes | Yes | No | No | Yes | No |
| scroll-insensitive | Yes | Yes | No | No | No | No |

> **Note**:
> 
> Internal refers to DML operations executed by the current ResultSet instance; External refers to DML operations executed by other connections or Statements.

### Positioning in Result Set

The ResultSet object has a cursor pointing to its current data row. Initially, the cursor is positioned before the first row. The next method moves the cursor to the next row; since this method returns false when there are no next rows in the ResultSet object, it can be used in a while loop to iterate through the result set. However, for scrollable result sets, the JDBC driver program provides more positioning methods, as follows:

#### Methods for Positioning in Result Set

|Method |Description                                         |
| --------------- | ------------------------------------------------------------ |
| next()          | Moves the ResultSet down by one row.                         |
| previous()      | Moves the ResultSet up by one row.                           |
| beforeFirst()   | Positions the ResultSet before the first row.                |
| afterLast()     | Positions the ResultSet after the last row.                  |
| first()         | Positions the ResultSet to the first row.                    |
| last()          | Positions the ResultSet to the last row.                     |
| absolute(int)   | Moves the ResultSet to the row specified by the parameter.   |
| relative(int)   | Moves forward or backward by the number of rows specified by the parameter. |

#### Get Result Set Cursor Position

|Method |Description                  |
| --------------- | ------------------------------------- |
| isFirst()       | Checks if it is on the first row.             |
| isLast()        | Checks if it is on the last row.      |
| isBeforeFirst() | Checks if it is before the first row. |
| isAfterLast()   | Checks if it is after the last row.   |
| getRow()        | Gets the current row number.          |

#### Get Data in Result Set

|Method                                        |Description                       |
| ------------------------------------------------------ | ------------------------------------------ |
| boolean getBoolean(int columnIndex)                    | Retrieves boolean data by column index.    |
| boolean getBoolean(String columnLabel)                 | Retrieves boolean data by column name.     |
| byte getByte(int columnIndex)                          | Retrieves byte data by column index.       |
| byte getByte(String columnLabel)                       | Retrieves byte data by column name.        |
| short getShort(int columnIndex)                        | Retrieves short data by column index.      |
| short getShort(String columnLabel)                     | Retrieves short data by column name.       |
| long getLong(int columnIndex)                          | Retrieves long data by column index.       |
| long getLong(String columnLabel)                       | Retrieves long data by column name.        |
| float getFloat(int columnIndex)                        | Retrieves float data by column index.      |
| float getFloat(String columnLabel)                     | Retrieves float data by column name.       |
| double getDouble(int columnIndex)                      | Retrieves double data by column index.     |
| double getDouble(String columnLabel)                   | Retrieves double data by column name.      |
| BigDecimal getBigDecimal(int columnIndex)              | Retrieves BigDecimal data by column index. |
| BigDecimal getBigDecimal(String columnLabel)           | Retrieves BigDecimal data by column name.  |
| byte[] getBytes(int columnIndex)                       | Retrieves byte[] data by column index.     |
| byte[] getBytes(String columnLabel)                    | Retrieves byte[] data by column name.      |
| int getInt(int columnIndex)                            | Retrieves int data by column index.        |
| int getInt(String columnLabel)                         | Retrieves int data by column name.         |
| RowId getRowId(int columnIndex)                        | Retrieves RowId data by column index.      |
| RowId getRowId(String columnLabel)                     | Retrieves RowId data by column name.       |
| String getString(int columnIndex)                      | Retrieves String data by column index.     |
| String getString(String columnLabel)                   | Retrieves String data by column name.      |
| Time getTime(int columnIndex) throws SQLException;     | Retrieves Time data by column index.       |
| Time getTime(String columnLabel) throws SQLException;  | Retrieves Time data by column name.        |
| Timestamp getTimestamp(int columnIndex)                | Retrieves Timestamp data by column index.  |
| Timestamp getTimestamp(String columnLabel)             | Retrieves Timestamp data by column name.   |
| Date getDate(int columnIndex)                          | Retrieves Date data by column index.       |
| Date getDate(String columnLabel)                       | Retrieves Date data by column name.        |

#### Get Streaming Result Set

A streamed result set fetches query results in a streaming manner, which can significantly improve data retrieval efficiency for large result sets. The streaming result set is enabled when stmt is set with fetchSize to Integer.MIN_VALUE, result set type is ResultSet.TYPE_FORWARD_ONLY, and resultSetConcurrency is ResultSet.CONCUR_READ_ONLY.

***Example***:

```java
Properties info = new Properties();
info.setProperty("user", "username");
info.setProperty("password", "password");
Connection conn= DriverManager.getConnection(url, info);

Statement stmt = conn.createStatement(ResultSet.TYPE_FORWARD_ONLY, ResultSet.CONCUR_READ_ONLY);

stmt.setFetchSize(Integer.MIN_VALUE);
```

You can also configure the connection through URL; when configured, the created statement under this connection will perform streaming fetch by default, example:

```java
String url = "jdbc:yasdb://192.168.1.2:1688/yasdb?defaultRowFetchSize=-2147483648&netTimeoutForStreamingResults=120";
Properties info = new Properties();
info.setProperty("user", "username");
info.setProperty("password", "password");
Connection conn= DriverManager.getConnection(url, info);

Statement stmt = conn.createStatement();
```

|Parameter            |Description                                         |
| ----------------------------- | ------------------------------------------------------------ |
| defaultRowFetchSize           | Number of rows fetched at a time during statement execution. Default is 10 (use driver default), set to a positive integer to control the number of rows fetched from the database each time. |
| netTimeoutForStreamingResults | After starting the streaming result set, this is the blocking timeout for server sending, in seconds. The default value is 600 seconds. Set to a positive integer; the server will disconnect after timeout. |

> **Note**:
>
> Streaming Result Set Use Cases:
> * Advantages: Streaming result sets can quickly fetch data when the result set data volume is large, significantly reducing memory usage.
> * Limitations:
>   * Can only scroll forward (TYPE_FORWARD_ONLY), does not support scrolling; cannot get row numbers; does not support bidirectional traversal.
>   * Streaming result sets cannot have secondary interactions before data fetching ends. Data types that require secondary interactions, such as LOB and cursor, are not suitable for streaming result sets.
> * Notes: Need to complete data reading before network disconnection or result set closure, otherwise may cause connection exceptions.

The complete demo code example is as follows:

```java
package quickstart;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.Statement;
import java.sql.SQLException;
import java.util.Properties;

public class ResultSetDemo {

	//Database connection parameters, replace with your own configuration.
    private static final String URL = "jdbc:yasdb://192.168.1.2:1688/yasdb";
    private static final String USER = "sales";
    private static final String PASSWORD = "sales";

    public static void main(String[] args) {

    	//Declare resource objects: Connection/Statement/ResultSet
        Connection conn = null;
        Statement stmt = null;
        ResultSet rs = null;

        try {
        	//Get the database connection
            Properties info = new Properties();
            info.setProperty("user", USER);
            info.setProperty("password", PASSWORD);
            conn = DriverManager.getConnection(URL, info);

            //Create a Statement object via the Connection.createStatement() method
            stmt = conn.createStatement();

            //Example: Execute insert operation
            String insertSql = "INSERT INTO customer(id, name) VALUES (2, 'Tom')";
            //Execute insert operation and return the number of affected rows
            int affectedRows = stmt.executeUpdate(insertSql);
            System.out.println("Data inserted successfully, number of affected rows: " + affectedRows);
            
            //Example: Execute query
            String querySql = "SELECT id, name FROM customer WHERE id = 2";
            
            //Execute query operation and return the result set
            rs = stmt.executeQuery(querySql);
            
            //Process the result set
            while (rs.next()) {
                int id = rs.getInt("id");
                String name = rs.getString("name");
                System.out.println("Query Result: ID=" + id + ", Name=" + name);
            }

        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
        	//Close resources (Order: ResultSet → Statement → Connection)
            try {
                if (rs != null) rs.close();
                if (stmt != null) stmt.close();
                if (conn != null) conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```

#### Set Result Set Data Sensitivity

When resultSetConcurrency is ResultSet.CONCUR_READ_ONLY and resultSetType is ResultSet.TYPE_SCROLL_SENSITIVE, theoretically you can see the modified results by scrolling back to the modified row after data changes. However, due to internal implementation limitations, features like views do not support this behavior. When this parameter is enabled, even if resultSetType is ResultSet.TYPE_SCROLL_SENSITIVE, edits made to updatable result sets are not displayed, meaning the result set becomes insensitive to data modifications.

To enable, modify readOnlyResultSetInsensitive=TRUE in the Connection URL. This parameter defaults to FALSE, meaning all read-only result sets are data-sensitive.

***Example***:

```java
String url = "jdbc:yasdb://192.168.1.2:1688/yashan?readOnlyResultSetInsensitive=true";
Properties info = new Properties();
info.setProperty("user", "username");
info.setProperty("password", "password");
Connection conn = DriverManager.getConnection(url, info);

Statement stmt = conn.createStatement();
```

|Parameter            |Description                                         |
| ----------------------------- | ------------------------------------------------------------ |
| readOnlyResultSetInsensitive  | Whether read-only result sets ignore data changes, TRUE ignores changes, FALSE perceives changes, default FALSE. |
### Set Result Set Type

Different types of result sets have their respective application scenarios, and applications need to select the appropriate result set type based on actual circumstances. In the process of executing SQL statements, it is required to first create the corresponding statement object. Some methods for creating statement objects provide functionality to set the result set type. The relevant Connection methods are as follows:

```java
//Create a Statement object.
createStatement();
 
//Create a PreparedStatement object.
prepareStatement(String sql);
 
//Create a CallableStatement object.
prepareCall(String sql);
```

The result set type descriptions are as follows:

|Parameter   |Description                                         |
| -------------------- | ------------------------------------------------------------ |
| resultSetType        | Indicates the type of the result set. Specifically, there are three types:<br>\*   ResultSet.TYPE_FORWARD_ONLY: ResultSet can only move forward, is the default value.<br>\*   ResultSet.TYPE_SCROLL_SENSITIVE: Can scroll back to the modified row after modification to see the modified result.<br>\*   ResultSet.TYPE_SCROLL_INSENSITIVE: Edits made to editable routines are not displayed. |
| resultSetConcurrency | Indicates the concurrency of the result set, specifically two types:<br>\*   ResultSet.CONCUR_READ_ONLY: Cannot update data in the result set unless establishing a new update statement from data in the result set.<br>\*   ResultSet.CONCUR_UPDATEABLE: Editable result set. For scrollable result sets, appropriate changes can be made to the result set. |

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
| isFirst()       | Checks if it is on a row.             |
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

The streaming result set is enabled when stmt is set with fetchSize to Integer.MIN_VALUE, result set type is ResultSet.TYPE_FORWARD_ONLY, and resultSetConcurrency is ResultSet.CONCUR_READ_ONLY.

***Example***: 

```java
Connection conn= DriverManager.getConnection(url, user, passwd);

Statement stmt = conn.createStatement(ResultSet.TYPE_FORWARD_ONLY, ResultSet.CONCUR_READ_ONLY);
stmt.setFetchSize(Integer.MIN_VALUE);
```

You can also configure the connection through URL; when configured, the created statement under this connection will perform streaming fetch by default, example:

```java
String url = "jdbc:yasdb://192.168.1.2:1688/yashan?defaultRowFetchSize=-2147483648&netTimeoutForStreamingResults=120";

Connection conn= DriverManager.getConnection(url, user, passwd);

Statement stmt = conn.createStatement();
```

|Parameter            |Description                                         |
| ----------------------------- | ------------------------------------------------------------ |
| defaultRowFetchSize           | Number of rows fetched at a time during statement execution  |
| netTimeoutForStreamingResults | Blocking timeout for the server's sending after starting streaming results |

#### Set Result Set Data Sensitivity

When resultSetConcurrency is ResultSet.CONCUR_READ_ONLY and resultSetType is ResultSet.TYPE_SCROLL_SENSITIVE, theoretically you can see the modified results by scrolling back to the modified row after data changes. However, due to internal implementation limitations, features like views do not support this behavior. When this parameter is enabled, even if resultSetType is ResultSet.TYPE_SCROLL_SENSITIVE, edits made to updatable result sets are not displayed, meaning the result set becomes insensitive to data modifications.

To enable, modify readOnlyResultSetInsensitive=TRUE in the Connection URL. This parameter defaults to FALSE, meaning all read-only result sets are data-sensitive.

***Example***:

```java
String url = "jdbc:yasdb://192.168.1.2:1688/yashan?readOnlyResultSetInsensitive=true";

Connection conn = DriverManager.getConnection(url, user, password);

Statement stmt = conn.createStatement();
```

| Parameter            | Description                                         |
| ----------------------------- | ------------------------------------------------------------ |
| readOnlyResultSetInsensitive  | Whether read-only result sets are sensitive to data modifications, TRUE insensitive, FALSE sensitive, default FALSE. |
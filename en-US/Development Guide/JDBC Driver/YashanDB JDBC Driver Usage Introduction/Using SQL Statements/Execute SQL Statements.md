### Ordinary SQL

JDBC applications manipulate database data by executing SQL statements (without passing parameters) following these steps:

1. Call the createStatement method of the Connection to create a statement object.

   ```java
   Statement stmt = con.createStatement();
   ```

2. Call the execute method of the Statement to execute the SQL statement.

   ```java
   stmt.execute("CREATE TABLE table_example(id INTEGER, name VARCHAR(32))");
   ```

3. Close the statement object.

   ```java
   stmt.close();
   ```

### SQL with Bound Parameters

JDBC applications manipulate database data by executing SQL statements (with bound parameters) following these steps:

1. Call the prepareStatement method of the Connection to create a statement object.

   ```java
   PreparedStatement pstmt = conn.prepareStatement("insert into tb1(col1) values(?)");
   ```

2. Call the set method to pass parameters.

   ```java
   pstmt.setObject(1, 1);
   ```

3. Call the execute method of the PreparedStatement to execute the SQL statement.

   ```java
   pstmt.execute();
   ```

4. Close the statement object.

   ```java
   pstmt.close();
   ```

> **Note**:
>
> When passing strings using the pstmt.setString interface, note that since YashanDB introduced the [variable spying](../../../PL Reference Manual/Parameters/Binding Parameters) functionality in version 23.1 and later, the logic of type inference for parameters to be bound in the statement has changed. If executing the following use case in version 23.1 and later, the WHERE condition will not match successfully, while it can match successfully in versions prior to 23.1, leading to different query results.

```java
  Statement stmt = conn.createStatement();
  stmt.execute("CREATE TABLE T1(id INT, data CHAR(10))");
  stmt.execute("INSERT INTO T1 VALUES(1, 'abc')");
  stmt.execute("COMMIT");
  
  PreparedStatement pstmt = conn.prepareStatement("select id from t1 where data=?");
  pstmt.setString(1, "abc");
  pstmt.execute();
  // Process the query results
```

For bound parameter execution scenarios, the JDBC client will first send a prepare command to the server, then send the bound parameter execution. You can enable the support for direct execution of bound parameters, allowing the SQL statement of prepareStatement and the parameters to be bound to be sent to the server at once, reducing interaction frequency. The enabling methods are:

|Configuration/Interface        |Description                                         |Example                                             |
| -------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| clientPrepare configuration parameter        | To enable direct execution with bound parameters<br/>* true: means enabled<br/>* false: means disabled | jdbc:yasdb://192.168.1.2:1688/yashan?clientPrepare=true      |
| void setClientPrepare(boolean clientPrepare) | Extension method of the YasConnection interface              | Connection connection = DriverManager.getConnection(url); ((YasConnection)connection).setClientPrepare(true); |

***Example***: 

```java
  Connection conn = getConnection("sales", "sales");
  ((YasConnection)conn).setClientPrepare(true);
  PreparedStatement preparedStatement = conn.prepareStatement("select ? from dual");

  Clob clob = conn.createClob();
  String testLobString = "test clob direct bind";
  clob.setString(1, testLobString);
  preparedStatement.setClob(1, clob);
  preparedStatement.execute();

  ResultSet resultSet = preparedStatement.getResultSet();
  resultSet.next();
  Clob clob1 = resultSet.getClob(1);
  clob.free();
  resultSet.close();

  preparedStatement.setInt(1,1);
  preparedStatement.execute();
  
  resultSet = preparedStatement.getResultSet();
  resultSet.next();
  int iValue1 = resultSet.getInt(1);
  resultSet.close();

  preparedStatement.close();
  conn.close();
```

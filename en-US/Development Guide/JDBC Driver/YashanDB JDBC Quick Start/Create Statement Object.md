Statement objects and PreparedStatement objects are both interfaces used for executing SQL statements. The difference is that Statement is used to execute static SQL statements, which need to be parsed and compiled again each time they are executed; while PreparedStatement is a pre-compiled SQL statement object that supports parameterized queries, can reuse execution plans, and can effectively prevent SQL injection and improve execution efficiency. **It is recommended to use PreparedStatement objects first**.

### Statement Object

java.sql.Statement is an interface provided by JDBC for executing static SQL statements and returning the execution results. It is created by a Connection object, and its primary function is to send pre-written SQL statements to the database for execution. A Statement object is created via the createStatement() method and is used to send simple SQL statements for querying or updating the database. The Statement object provides three methods to execute SQL statements:

1. The execute method is used to execute SQL statements that return multiple result sets or update multiple tuples.
2. The executeQuery method is used to execute SQL statements that return a single result set.
3. The executeUpdate method is used to execute SQL statements containing INSERT, UPDATE, or DELETE clauses, or SQL statements that return no content, such as DDL statements.

Example of creating a Statement object:

1. Call the createStatement method of the Connection to create a statement object.

   ```java
   Statement stmt = conn.createStatement();
   ```

2. Call the execute method of the Statement to execute the SQL statement.

   ```java
   stmt.execute("CREATE TABLE table_example(id INTEGER, name VARCHAR(32))");
   ```

3. Close the statement object.

   ```java
   stmt.close();
   ```

### PrepareStatement Object

java.sql.PreparedStatement is a subinterface of Statement in JDBC, specifically designed for executing precompiled SQL statements. It addresses two core issues of Statement: the risk of SQL injection and the poor performance caused by repeated execution of SQL statements.

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
> YashanDB 23.1 and later versions introduce the [variable peeking](../../PL Reference Manual/Parameters/Binding Parameters) feature. When using pstmt.setString to pass strings, the default type inference logic for binding parameters has changed, which may cause WHERE condition matching results to differ from earlier versions. For scenarios requiring precise matching, it is recommended to use CAST function to explicitly specify type: `SELECT id FROM t1 WHERE data = CAST(? AS CHAR(10))`.


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
The complete demo code example is as follows:

```java
package quickstart;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.Statement;
import java.sql.SQLException;

public class StatementDemo {
	
	//Database connection parameters, replace with your own configuration.
    private static final String URL = "jdbc:yasdb://192.168.1.2:1688/yasdb";
    private static final String USER = "sales";
    private static final String PASSWORD = "sales";

    public static void main(String[] args) {
    	
    	//Declare resource objects: Connection/Statement/ResultSet
        Connection conn = null;
        Statement stmt = null;
        
        try {
        	//Get the database connection
            conn = DriverManager.getConnection(URL, USER, PASSWORD);

            //Create a Statement object via the Connection.createStatement() method
            stmt = conn.createStatement();

            //Create customer table
            stmt.execute("CREATE TABLE IF NOT EXISTS customer(id INTEGER, name VARCHAR(32))");

            //Execute insert operation
            String insertSql = "INSERT INTO customer(id, name) VALUES (2, 'Tom')";
            //Execute insert operation and return the number of affected rows
            int affectedRows = stmt.executeUpdate(insertSql);
            System.out.println("Data inserted successfully, number of affected rows: " + affectedRows);
            
  
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
        	//Close resources (Order: ResultSet → Statement → Connection)
            try {
                if (stmt != null) stmt.close();
                if (conn != null) conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```
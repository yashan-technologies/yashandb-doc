This article provides a complete example to demonstrate the application development steps for creating tables based on YashanDB JDBC.

```java
package quickstart;

//TableCreateExample.java

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.SQLException;
import java.sql.Statement;
import java.sql.CallableStatement;

public class TableCreateExample {
    //Create database connection.
    public static Connection getConnection(String username, String passwd) {
        String driver = "com.yashandb.jdbc.Driver";
        String sourceURL = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Connection conn = null;
        try {
            //Load database driver.
            Class.forName(driver);
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }

        try {
            //Create database connection.
            conn = DriverManager.getConnection(sourceURL, username, passwd);
            System.out.println("Connection succeed!");
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }

        return conn;
    }
 
 
    public static void createTable(Connection conn) {
        Statement stmt = null;
        try {
            stmt = conn.createStatement();

            //Execute normal SQL statement.
            stmt.execute("CREATE TABLE customer(id INTEGER, name VARCHAR(32))");
            System.out.println("create table customer succeed!");
            stmt.close();
        } catch (SQLException e) {
            if (stmt != null) {
                try {
                    stmt.close();
                } catch (SQLException e1) {
                    e1.printStackTrace();
                }
            }
            e.printStackTrace();
        }
    }
 

    public static int execJdbcexample() throws SQLException {
        //Create the database connection.
        Connection conn = getConnection("sales", "sales");

        //Call the function of table creating.
        createTable(conn);

        //Close the database connection.
        try {
            conn.close();
        } catch (SQLException e) {
            e.printStackTrace();
            return 0;
        } finally {
            conn.close();
            return 1;
       }
    }

     /**
     * Main program, calling static method of table creating.
     * @param args
     * @throws SQLException 
     */
    public static void main(String[] args) throws SQLException {
       int a = execJdbcexample();
    }
}
```

Compile in a Linux environment:

```shell
$ javac TableCreateExample.java
```

Execute:

```shell
$ java quickstart.TableCreateExample
Connection succeed!
create table customer succeed!
```

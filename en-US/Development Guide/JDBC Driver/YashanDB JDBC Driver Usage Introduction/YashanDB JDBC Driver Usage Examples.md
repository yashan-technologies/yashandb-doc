This article provides a complete example demonstrating the main steps for application development based on YashanDB JDBC, including operations such as creating databases, creating tables, and inserting data. 

```java
package jdbc0;

//Jdbcexample.java

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.SQLException;
import java.sql.Statement;
import java.sql.CallableStatement;

public class Jdbcexample {
    //Create database connection.
    public static Connection getConnection(String username, String passwd) {
        String driver = "com.yashandb.jdbc.Driver";
        String sourceURL = "jdbc:yasdb://127.0.0.1:1688/ya";
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
 
    //Execute normal SQL statement to create customer table.
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
 
   //Execute prepared statement to batch insert data.
   public static void batchInsertData(Connection conn) {
        PreparedStatement pst = null;

        try {
            //Generate prepared statement.
            pst = conn.prepareStatement("INSERT INTO customer VALUES (?,?)");
            for (int i = 0; i < 3; i++) {
                //Add parameters.
                pst.setInt(1, i);
                pst.setString(2, "sales" + i);
                pst.addBatch();
            }
            //Execute batch.
            pst.executeBatch();
            System.out.println("insert table customer succeed!");
            pst.close();
        } catch (SQLException e) {
            if (pst != null) {
                try {
                    pst.close();
                } catch (SQLException e1) {
                    e1.printStackTrace();
                }
            }
            e.printStackTrace();
        }
    }
 
  public static int execJdbcexample(String ctrls) {
        //Create database connection.
        Connection conn = getConnection("sales", "sales");

        //Create table.
        createTable(conn);

        //Batch insert data.
        batchInsertData(conn);

        //Close database connection.
        try {
            conn.close();
        } catch (SQLException e) {
            e.printStackTrace();
            return 0;
        }
        return 1;
    }

     /**
     * Main program, calling each static method step by step.
     * @param args
     */
    public static void main(String[] args) {
       int a = execJdbcexample("1");
    }
}
```

Compile in a Linux environment:

```shell
$ javac -d . Jdbcexample.java
```

Execute:

```shell
$ java jdbc0.Jdbcexample
Connection succeed!
create table customer succeed!
insert table customer succeed!
```

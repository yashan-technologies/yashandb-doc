This article provides a complete example demonstrating how to perform data delete operations on a table using YashanDB JDBC.

```java
package quickstart;

//TableDeleteDataExample.java

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.SQLException;
import java.sql.Statement;
import java.sql.CallableStatement;

public class TableDeleteDataExample {
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

   //Execute prepared statement to update data.
   public static void UpdateData(Connection conn) {
        PreparedStatement pst = null;

        try {
            //Generate prepared statement.
            String updateSql = "UPDATE customer SET name = ? WHERE id = 1";
            pst = conn.prepareStatement(updateSql);
            pst.setString(1, "sales001"); 
            // Execute the update operation and return the number of affected rows.
            int affectedRows = pst.executeUpdate();
            System.out.println("update table customer where id=1 succeed!");
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
 
   //Execute prepared statement to delete data.
   public static void DeleteData(Connection conn) {
        PreparedStatement pst = null;

        try {
            //Generate prepared statement.
            String updateSql = "DELETE customer WHERE id = ?";
            pst = conn.prepareStatement(updateSql);
            //Delete table data where id is 1
            pst.setInt(1, 1); 
            // Execute the delete operation
            pst.execute();
            System.out.println("Delete table customer where id=1 succeed!");
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
   
  public static int execJdbcexample() {
        //Create database connection.
        Connection conn = getConnection("sales", "sales");

        //Create table.
        //createTable(conn);

        //Delete data.
        DeleteData(conn);

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
       int a = execJdbcexample();
    }
}
```

Compile in a Windows environment:

```shell
$ javac TableCreateExample.java
```

Execute:

```shell
$ java quickstart.TableDeleteDataExample
Connection succeed!
Delete table customer where id=1 succeed!
```

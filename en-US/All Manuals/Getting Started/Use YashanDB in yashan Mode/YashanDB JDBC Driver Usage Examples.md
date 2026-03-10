This chapter will introduce the installation and basic operation demonstration of the YashanDB JDBC driver.

## Example Environment Introduction

|Software |Version |
| -------- | --------------------- |
| JDBC driver      | yashandb-jdbc-{version}.jar |
| JDK      | 1.8                   |
| JRE      | 1.8                   |

## Preparations Before Integration

1. Before using the JDBC driver, you need to install the JDK. The compatibility information for JDK versions is as follows; please install the versions listed below:
   - JDK: 1.8 and above
   - JRE: 1.8 and above

2. You can verify if the Java environment is working properly by executing the command `java -version`:

   ```bash
   $ java -version
   openjdk version "1.8.0_372"
   OpenJDK Runtime Environment (build 1.8.0_372-b07)
   OpenJDK 64-Bit Server VM (build 25.372-b07, mixed mode)
   ```

3. Please contact our technical support to obtain the JDBC driver software package. An example package name is: `yasdb-jdbc-version.jar`.

## JDBC Driver Installation

1. Execute the following commands to create the `/home/yashan/JDBC` directory. Please pay attention to case sensitivity:

   ```shell
   $ mkdir JDBC
   $ cd JDBC
   ```

2. Upload the JDBC software package to this directory.

3. Configure the JDBC driver environment:

   1. Execute the following command to open the file `~/.bashrc` using the vi editor:

      ```bash
      $ vi ~/.bashrc
      ```

   2. Press the `i` key to enter input mode and input the following content into the file. Please replace `yasdb-jdbc-{version}.jar` with the actual package name:

      ```bash
      export CLASSPATH=/home/yashan/JDBC/yasdb-jdbc-{version}.jar:${CLASSPATH} 
      ```

   3. Press the `Esc` key to exit input mode, and then type `:wq` to save and close the file.

   4. Execute the following to activate the environment variable:

      ```bash
      $ source ~/.bashrc
      ```

## Writing JAVA File

1. Execute the following command to create the Java file `Jdbcexample.java`:

   ```shell
   $ vi Jdbcexample.java
   ```

2. Press the `i` key to enter input mode and input the following content into the file:

   ```java
   package jdbc0;
   import java.sql.Connection;
   import java.sql.DriverManager;
   import java.sql.PreparedStatement;
   import java.sql.SQLException;
   import java.sql.Statement;
   import java.sql.CallableStatement;
   
   public class Jdbcexample {
       public static Connection getConnection(String username, String passwd) {
           String driver = "com.yashandb.jdbc.Driver";
           String sourceURL = "jdbc:yasdb://host:port/database_name";
           Connection conn = null;
           try {
               Class.forName(driver);
           } catch (Exception e) {
               e.printStackTrace();
               return null;
           }
   
           try {
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
               stmt.execute("DROP TABLE IF EXISTS customer");
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
    
      public static void batchInsertData(Connection conn) {
           PreparedStatement pst = null;
   
           try {
               pst = conn.prepareStatement("INSERT INTO customer VALUES (?,?)");
               for (int i = 0; i < 3; i++) {
                   pst.setInt(1, i);
                   pst.setString(2, "sales" + i);
                   pst.addBatch();
               }
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
           Connection conn = getConnection(user, password);
   
           createTable(conn);
   
           batchInsertData(conn);
   
           try {
               conn.close();
           } catch (SQLException e) {
               e.printStackTrace();
               return 0;
           }
           return 1;
       }
   
       public static void main(String[] args) {
          int a = execJdbcexample("1");
       }
   }
   ```

   Among them:

   - `String sourceURL = "jdbc:yasdb://host:port/database_name";` is the database connection descriptor. Please replace `host:port` with the IP address and port of the server, and `database_name` with the name of the database.

   - `Connection conn = getConnection(user, password);` is used to create the connection. Please replace `user` with the database connection username and `password` with the user's password.

3. Execute the following command to compile:

   ```shell
   $ javac -d . Jdbcexample.java
   ```

4. Execute the following command to run the program. If you see the following output, it indicates that the database connection was successful:

   ```shell
   $ java -Djdbc.drivers=com.yashandb.jdbc.Driver jdbc0.Jdbcexample
   Connection succeed!
   create table customer succeed!
   insert table customer succeed!
   ```

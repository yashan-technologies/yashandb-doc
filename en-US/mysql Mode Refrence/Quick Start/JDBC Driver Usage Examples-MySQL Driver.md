This chapter will introduce the connection using the MySQL JDBC driver.

## Sample Environment Introduction

|Software |Version |
| -------- |------------------------------|
| JDBC driver        | mysql-connector-java-8.*.jar   |
| JDK      | 1.8                          |
| JRE      | 1.8                          |

## Preparation Before Integration

1. Before using the JDBC driver, you must first install the JDK. The compatibility information for JDK versions is as follows; please install the versions mentioned below:
   - JDK: 1.8 and above
   - JRE: 1.8 and above

2. Completed the Schema creation operation in [Basic SQL Operations](../../All Manuals/Reference Manual of mysql Mode/Quick Start/使用SQL语言进行数据库操作).

   ```sql
   CREATE DATABASE example1;   
   USE example1;
   ```

>**Note**
>
> YashanDB (mysql mode) is compatible with MySQL drivers of two major versions, 5.\* and 8.\*. This example uses the MySQL driver of version 8.\*, so JDK and JRE versions of 1.8 or higher are required. If the MySQL driver of version 5.\* is used, JDK and JRE versions of 1.6 or higher will suffice.

2. You can verify whether the Java environment is working normally by executing the `java -version` command:

   ```bash
   $ java -version
   openjdk version "1.8.0_372"
   OpenJDK Runtime Environment (build 1.8.0_372-b07)
   OpenJDK 64-Bit Server VM (build 25.372-b07, mixed mode)
   ```

3. Please obtain the MySQL JDBC driver package on your own, or you can contact our technical support for it. An example of the package name is: `mysql-connector-java-VERSIONNUMBER.jar`.

## JDBC Driver Installation

1. Execute the following command to create the `/home/yashan/JDBC` directory; please note case sensitivity:

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

   2. Press the `i` key to enter input mode and input the following content into the file. Please replace `mysql-connector-java-{version_number}.jar` with the actual package name:

      ```bash
      export CLASSPATH=/home/yashan/JDBC/mysql-connector-java-{version_number}.jar:${CLASSPATH} 
      ```

   3. Press the `Esc` key to exit input mode, then type `:wq` to save and close the file.

   4. Execute the following to make the environment variable effective:

      ```bash
      $ source ~/.bashrc
      ```

## Writing the JAVA File

1. Execute the following command to create the Java file `Jdbcexample.java`.

   ```shell
   $ vi Jdbcexample.java
   ```

2. Press the `i` key to enter input mode and input the following content into the file.

   ```java
   package jdbc0;
   
   import java.sql.Connection;
   import java.sql.DriverManager;
   import java.sql.PreparedStatement;
   import java.sql.SQLException;
   import java.sql.Statement;
   
   public class Jdbcexample {
      public static Connection getConnection(String username, String passwd) {
         String driver = "com.mysql.cj.jdbc.Driver"; // If using MySQL driver version 5.*, change com.mysql.cj.jdbc.Driver to com.mysql.jdbc.Driver
         String sourceURL = "jdbc:mysql://host:port/schema_name?allowPublicKeyRetrieval=true";
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
   
       public static int execJdbcexample() {
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
           int a = execJdbcexample();
       }
   }
   
   ```

   Among them:

   - `String sourceURL = "jdbc:mysql://host:port/database_name?allowPublicKeyRetrieval=true";` is the database connection descriptor. Replace `host:port` with the value of the mysql_addr parameter in the yashandb.toml file under the database installation path. If installed with default configuration, the host is the database server IP address, and the default port number is 1690; `schema_name` is the schema created through the `create database` statement;

   - `Connection conn = getConnection(user, password);` is used to create a connection. Please replace `user` with the database connection username and `password` with that user's password.

   >**Note**
   >
   > - When using a user of the yashan mode (for example, the built-in sys user), the username must be in uppercase (except in cases where it is created with double quotes), and the connection string must include the parameter allowPublicKeyRetrieval=true.
   > 
   > - When using a user of the mysql mode, login should be done with the original case.

3. Execute the following command to compile:

   ```shell
   $ javac -d . Jdbcexample.java
   ```

4. Execute the following command to run the program. If the output is as follows, it indicates that the database connection was successful.
   ```shell
   $ java jdbc0.Jdbcexample
   Connection succeed!
   create table customer succeed!
   insert table customer succeed!
   ```

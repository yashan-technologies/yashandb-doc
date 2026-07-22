本章将介绍使用MySQL JDBC驱动连接YashanDB的操作演示。

## 示例环境介绍

|  软件| 版本|
| -------- |------------------------------|
| JDBC驱动 | mysql-connector-java-8.*.jar |
| JDK      | 1.8                          |
| JRE      | 1.8                          |

## 对接前准备

1. 使用JDBC驱动前须先安装JDK，JDK版本兼容信息如下，请自行安装下述版本的环境：
   - JDK：1.8及以上
   - JRE：1.8及以上

2. 已完成[使用SQL语言进行数据库操作](使用SQL语言进行数据库操作)中的创建Schema操作。

   ```sql
   CREATE DATABASE example1;   
   USE example1;
   ```

>**Note**
>
> YashanDB（mysql模式）可兼容5.\*和8.\*两个大版本的MySQL驱动，本示例以8.\*版本的MySQL驱动为例，故而要求JDK和JRE版本为1.8及以上，若使用5.\*版本的MySQL驱动，则JDK和JRE版本为1.6及以上即可。

2. 可通过执行`java -version`命令验证Java环境是否正常：

   ```bash
   $ java -version
   openjdk version "1.8.0_372"
   OpenJDK Runtime Environment (build 1.8.0_372-b07)
   OpenJDK 64-Bit Server VM (build 25.372-b07, mixed mode)
   ```

3. 请自行获取MySQL的jdbc驱动包，也可联系我们的技术支持获取，软件包名称示例： `mysql-connector-java-版本号.jar` 。

## JDBC驱动安装

1. 执行如下命令创建`/home/yashan/JDBC`目录，请注意区分大小写：

   ```shell
   $ mkdir JDBC
   $ cd JDBC
   ```

2. 将JDBC软件包上传至该目录中。

3. 配置JDBC驱动环境：

   1. 执行如下命令使用vi编辑器打开文件`~/.bashrc`：

      ```bash
      $ vi ~/.bashrc
      ```

   2. 通过`i`键打开输入模式，并将如下内容输入至文件中，请将`mysql-connector-java-{version_number}.jar`更换成实际软件包名称：

      ```bash
      export CLASSPATH=/home/yashan/JDBC/mysql-connector-java-{version_number}.jar:${CLASSPATH} 
      ```

   3. 通过`Esc`键退出输入模式，然后输入`:wq`保存并关闭文件。

   4. 执行如下生效环境变量：

      ```bash
      $ source ~/.bashrc
      ```

## 编写JAVA文件

1. 执行如下命令创建Java文件`Jdbcexample.java`.

   ```shell
   $ vi Jdbcexample.java
   ```

2. 通过`i`键打开输入模式，并将如下内容输入至文件中。

   ```java
   package jdbc0;
   
   import java.sql.Connection;
   import java.sql.DriverManager;
   import java.sql.PreparedStatement;
   import java.sql.SQLException;
   import java.sql.Statement;
   
   public class Jdbcexample {
      public static Connection getConnection(String username, String passwd) {
         String driver = "com.mysql.cj.jdbc.Driver";// 如果使用mysql驱动的5.*版本，请把com.mysql.cj.jdbc.Driver改成com.mysql.jdbc.Driver
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

   其中：

   - `String sourceURL = "jdbc:mysql://host:port/schema_name?allowPublicKeyRetrieval=true";`为数据库连接描述符。`host:port`更换成数据库安装路径下yashandb.toml文件中的mysql_addr的参数值，如按照默认配置进行安装，则主机为数据库服务器IP地址，端口号默认为1690；`schema_name`为通过`create database`语句创建的schema；

   - `Connection conn = getConnection(user, password);`用于创建连接，请将`user`更换成数据库连接用户名，`password`更换成该用户的密码。

   >**Note**
   >
   > - 当使用的是yashan模式的用户（比如内置的sys用户）时，用户名必须为全大写（创建时带双引号的情况除外），且连接串里面必须加上allowPublicKeyRetrieval=true参数。
   > 
   > - 当使用的是mysql模式的用户时，应使用原大小写登录。

3. 执行如下命令进行编译：

   ```shell
   $ javac -d . Jdbcexample.java
   ```

4. 执行如下命令执行程序，如输出如下结果，则代表数据库连接成功。
   ```shell
   $ java jdbc0.Jdbcexample
   Connection succeed!
   create table customer succeed!
   insert table customer succeed!
   ```

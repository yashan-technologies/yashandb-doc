示例：基于JDBC接口开发应用程序

```java
package jdbc0;

//Jdbcexample.java
//演示基于JDBC开发的主要步骤，涉及创建数据库、创建表、插入数据等。

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.SQLException;
import java.sql.Statement;
import java.sql.CallableStatement;

public class Jdbcexample {
    //创建数据库连接。
    public static Connection getConnection(String username, String passwd) {
        String driver = "com.yashandb.jdbc.Driver";
        String sourceURL = "jdbc:yasdb://127.0.0.1:1688/ya";
        Connection conn = null;
        try {
            //加载数据库驱动。
            Class.forName(driver);
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }

        try {
            //创建数据库连接。
            conn = DriverManager.getConnection(sourceURL, username, passwd);
            System.out.println("Connection succeed!");
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }

        return conn;
    }
 
    //执行普通SQL语句，创建customer表。
    public static void createTable(Connection conn) {
        Statement stmt = null;
        try {
            stmt = conn.createStatement();

            //执行普通SQL语句。
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
 
   //执行预处理语句，批量插入数据。
   public static void batchInsertData(Connection conn) {
        PreparedStatement pst = null;

        try {
            //生成预处理语句。
            pst = conn.prepareStatement("INSERT INTO customer VALUES (?,?)");
            for (int i = 0; i < 3; i++) {
                //添加参数。
                pst.setInt(1, i);
                pst.setString(2, "sales" + i);
                pst.addBatch();
            }
            //执行批处理。
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
        //创建数据库连接。
        Connection conn = getConnection("sales", "sales");

        //创建表。
        createTable(conn);

        //批插数据。
        batchInsertData(conn);

        //关闭数据库连接。
        try {
            conn.close();
        } catch (SQLException e) {
            e.printStackTrace();
            return 0;
        }
        return 1;
    }

     /**
     * 主程序，逐步调用各静态方法。
     * @param args
     */
    public static void main(String[] args) {
       int a = execJdbcexample("1");
    }
}
```

以Linux环境为例编译：

```shell
$ javac -d . Jdbcexample.java
```

执行：

```shell
$ java jdbc0.Jdbcexample
Connection succeed!
create table customer succeed!
insert table customer succeed!
```

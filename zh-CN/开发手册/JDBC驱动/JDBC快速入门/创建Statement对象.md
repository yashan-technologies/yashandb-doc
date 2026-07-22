Statement对象和PreparedStatement对象都是用于执行SQL语句的接口，二者的区别在于：Statement用于执行静态SQL语句，每次执行时都需要重新解析和编译SQL；而PreparedStatement是预编译的SQL语句对象，支持参数化查询，可以重复使用执行计划，能够有效防止SQL注入并提升执行效率。**推荐优先使用PreparedStatement对象**。

### Statement对象

java.sql.Statement是JDBC提供的用于执行静态SQL语句并返回执行结果的接口，它由Connection对象创建，主要作用是将编写好的SQL语句发送给数据库执行。Statement对象由方法createStatement所创建，用于发送简单的SQL语句，来进行查询或对数据库进行更新，Statement对象提供三种方法执行SQL语句：

1. execute方法用于执行返回多个结果集或更新多个元组的SQL语句。
2. executeQuery方法用于执行返回单个结果集的SQL语句。
3. executeUpdate方法用于执行含有INSERT、UPDATE或DELETE语句或者不返回任何内容的SQL语句，如DDL语句。

创建Statement对象的示例如下：

1. 调用Connection的createStatement方法创建语句对象。

   ```java
   Statement stmt = conn.createStatement();
   ```

2. 调用Statement的execute方法执行SQL语句。

   ```java
   stmt.execute("CREATE TABLE table_example(id INTEGER, name VARCHAR(32))");
   ```

3. 关闭语句对象。

   ```java
   stmt.close();
   ```

### PrepareStatement对象

java.sql.PreparedStatement是JDBC中Statement的子接口，专门用于执行预编译的SQL语句。它解决了Statement的两大核心问题：SQL注入风险、重复执行SQL时的性能低效。

1. 调用Connection的prepareStatement方法创建语句对象。

   ```java
   PreparedStatement pstmt = conn.prepareStatement("insert into tb1(col1) values(?)");
   ```

2. 调用set方法传参数。

   ```java
   pstmt.setObject(1, 1);
   ```

3. 调用PreparedStatement的execute方法执行SQL语句。

   ```java
   pstmt.execute();
   ```

4. 关闭语句对象。

   ```java
   pstmt.close();
   ```

> **Note**:
>
> YashanDB 23.1及之后版本新增了[变量窥视](../../PL参考手册/参数/绑定参数)功能，使用pstmt.setString传入字符串时，绑定参数的默认类型推导逻辑有变化，可能导致WHERE条件匹配结果与之前版本不一致。对于需要精确匹配的场景，建议使用CAST函数显式指定类型：`SELECT id FROM t1 WHERE data = CAST(? AS CHAR(10))`。


对于绑定参数执行场景，JDBC客户端默认会先向服务端发送prepare命令，然后发送绑定参数执行。可以通过支持直接执行绑定参数配置，将prepareStatement的sql语句和要绑定的参数一次性发给服务端，减少交互次数。开启方式：

| 配置/接口| 说明| 示例|
| -------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| clientPrepare配置参数                        | 开启直接执行带绑定参数<br/>* true：表示开启<br/>* false：表示关闭 | jdbc:yasdb://192.168.1.2:1688/yashan?clientPrepare=true      |
| void setClientPrepare(boolean clientPrepare) | YasConnection接口扩展方法                                    | Connection connection = DriverManager.getConnection(url); ((YasConnection)connection).setClientPrepare(true); |

示例：

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
完整的示例代码示例如下：

```java
package quickstart;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.Statement;
import java.sql.SQLException;

public class StatementDemo {
	
    //数据库连接参数，替换为自己的配置
    private static final String URL = "jdbc:yasdb://192.168.1.2:1688/yasdb";
    private static final String USER = "sales";
    private static final String PASSWORD = "sales";

    public static void main(String[] args) {
    	
        //声明资源对象：Connection/Statement/ResultSet
        Connection conn = null;
        Statement stmt = null;
        
        try {
            //获取数据库连接
            conn = DriverManager.getConnection(URL, USER, PASSWORD);

            //创建Statement对象，通过Connection createStatement()方法创建
            stmt = conn.createStatement();

            //创建customer表
            stmt.execute("CREATE TABLE IF NOT EXISTS customer(id INTEGER, name VARCHAR(32))");

            //执行插入
            String insertSql = "INSERT INTO customer(id, name) VALUES (2, 'Tom')";
            //执行插入，返回受影响行数
            int affectedRows = stmt.executeUpdate(insertSql);
            System.out.println("Data inserted successfully, number of affected rows: " + affectedRows);
            
  
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            //关闭资源（顺序：ResultSet → Statement → Connection）
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
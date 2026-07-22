### 普通SQL

JDBC应用程序通过执行SQL语句来操作数据库的数据（不用传递参数的语句），按以下步骤进行：

1. 调用Connection的createStatement方法创建语句对象。

   ```java
   Statement stmt = con.createStatement();
   ```

2. 调用Statement的execute方法执行SQL语句。

   ```java
   stmt.execute("CREATE TABLE table_example(id INTEGER, name VARCHAR(32))");
   ```

3. 关闭语句对象。

   ```java
   stmt.close();
   ```

### 绑定参数的SQL

JDBC应用程序通过执行SQL语句来操作数据库的数据（绑定参数的语句），按以下步骤进行：

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
> 使用pstmt.setString接口传入字符串时，需注意，由于YashanDB在23.1及之后版本新增了[变量窥视](../../../PL参考手册/参数/绑定参数)功能，系统对于语句待绑定参数的预推导类型逻辑发生了变化。若在v23.1及之后版本中执行以下用例，WHERE条件将无法成功匹配，而v23.1之前版本则可以正常匹配，二者查询结果会有差异。

```java
  Statement stmt = conn.createStatement();
  stmt.execute("CREATE TABLE T1(id INT, data CHAR(10))");
  stmt.execute("INSERT INTO T1 VALUES(1, 'abc')");
  stmt.execute("COMMIT");
  
  PreparedStatement pstmt = conn.prepareStatement("select id from t1 where data=?");
  pstmt.setString(1, "abc");
  pstmt.execute();
  // 对查询结果进行处理
```

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

### ResultSet对象

java.sql.ResultSet是JDBC提供的用于存储和操作数据库查询结果的接口，它由Statement/PreparedStatement的executeQuery方法返回，本质是一个数据集，可以把它理解成一个指向查询结果的指针（游标），默认只能向前移动，逐行读取数据。

调用Statement的executeQuery方法创建语句对象的示例如下：

   ```java
   //创建一个Statement对象。
   stmt = conn.createStatement();

   //创建customer表
   stmt.execute("CREATE TABLE IF NOT EXISTS customer(id INTEGER, name VARCHAR(32))");

   //创建一个ResultSet对象。
   ResultSet rs = stmt.executeQuery("SELECT * FROM customer");
   ```

### 设置结果集类型

不同类型的结果集有各自的应用场景，应用程序需要根据实际情况选择相应的结果集类型。在执行SQL语句的过程中，都需要先创建相应的语句对象。而部分创建语句对象的方法提供了设置结果集类型的功能。涉及的Connection的方法如下：

结果集类型说明如下所示：

| 参数| 描述|
| -------------------- | ------------------------------------------------------------ |
| resultSetType        | 表示结果集的类型，具体有三种类型：<br>\*   ResultSet.TYPE\_FORWARD\_ONLY：ResultSet只能向前移动，是缺省值。<br>\*   ResultSet.TYPE\_SCROLL\_SENSITIVE：在修改后重新滚动到修改所在行，可以看到修改后的结果。<br>\*   ResultSet.TYPE\_SCROLL\_INSENSITIVE：对可修改例程所做的编辑不进行显示。 |
| resultSetConcurrency | 表示结果集的并发，具体有两种类型： <br>\*  ResultSet.CONCUR\_READ\_ONLY：如果不从结果集中的数据建立一个新的更新语句，不能对结果集中的数据进行更新。<br>\*  ResultSet.CONCUR\_UPDATEABLE：可改变的结果集。对于可滚动的结果集，可对结果集进行适当的改变。 |

不同ResultSet类型对数据修改的可见性如下：

| ResultSet 类型| 可见内部删除？| 可见内部更新？| 可见内部插入？| 可见外部删除？| 可见外部更新？| 可见外部插入？|
|---|---|---|---|---|---|---|
| forward-only | No | Yes | No | No | No | No |
| scroll-sensitive | Yes | Yes | No | No | Yes | No |
| scroll-insensitive | Yes | Yes | No | No | No | No |

> **Note**:
> 
> 内部指当前ResultSet实例执行的DML操作；外部指其他连接或Statement执行的DML操作。

### 在结果集中定位

ResultSet对象具有指向其当前数据行的光标。最初，光标被置于第一行之前。next方法将光标移动到下一行；因为该方法在ResultSet对象没有下一行时返回false，所以可以在while循环中使用它来迭代结果集。但对于可滚动的结果集，JDBC驱动程序提供更多的定位方法，如下所示：

#### 在结果集中定位的方法

| 方法| 描述|
| --------------- | ------------------------------------------------------------ |
| next()          | 把ResultSet向下移动一行。                                    |
| previous()      | 把ResultSet向上移动一行。                                    |
| beforeFirst()   | 把ResultSet定位到第一行之前。                                |
| afterLast()     | 把ResultSet定位到最后一行之后。                              |
| first()         | 把ResultSet定位到第一行。                                    |
| last()          | 把ResultSet定位到最后一行。                                  |
| absolute(int)   | 把ResultSet移动到参数指定的行数。                            |
| relative(int)   | 向前或者向后移动参数指定的行。                               |

#### 获取结果集光标位置

| 方法| 描述|
| --------------- | ------------------------------------- |
| isFirst()       | 是否在第一行。                          |
| isLast()        | 是否在最后一行。                      |
| isBeforeFirst() | 是否在第一行之前。                    |
| isAfterLast()   | 是否在最后一行之后。                  |
| getRow()        | 获取当前在第几行。                    |

#### 获取结果集中的数据

| 方法| 描述|
| ------------------------------------------------------ | ------------------------------------------ |
| boolean getBoolean(int columnIndex)                    | 按列标获取bool型数据。                     |
| boolean getBoolean(String columnLabel)                 | 按列名获取bool型数据。                     |
| byte getByte(int columnIndex)                          | 按列标获取Byte型数据。                     |
| byte getByte(String columnLabel)                       | 按列名获取Byte型数据。                     |
| short getShort(int columnIndex)                        | 按列标获取short型数据。                    |
| short getShort(String columnLabel)                     | 按列名获取short型数据。                    |
| long getLong(int columnIndex)                          | 按列标获取long型数据。                     |
| long getLong(String columnLabel)                       | 按列名获取long型数据                       |
| float getFloat(int columnIndex)                        | 按列标获取float型数据。                    |
| float getFloat(String columnLabel)                     | 按列名获取float型数据。                    |
| double getDouble(int columnIndex)                      | 按列标获取double型数据。                   |
| double getDouble(String columnLabel)                   | 按列名获取double型数据                     |
| BigDecimal getBigDecimal(int columnIndex)              | 按列标获取BigDecimal 型数据。              |
| BigDecimal getBigDecimal(String columnLabel)           | 按列名获取BigDecimal 型数据。              |
| byte\[\] getBytes(int columnIndex)                     | 按列标获取byte\[\]数据。                   |
| byte\[\] getBytes(String columnLabel)                  | 按列名获取byte\[\]数据。                   |
| int getInt(int columnIndex)                            | 按列标获取int型数据。                      |
| int getInt(String columnLabel)                         | 按列名获取int型数据。                      |
| RowId getRowId(int columnIndex)                        | 按列标获取RowId型数据。                    |
| RowId getRowId(String columnLabel)                     | 按列名获取RowId型数据。                    |
| String getString(int columnIndex)                      | 按列标获取String型数据。                   |
| String getString(String columnLabel)                   | 按列名获取String型数据。                   |
| Time getTime(int columnIndex) throws SQLException;     | 按列标获取Time型数据。                     |
| Time  getTime(String columnLabel) throws SQLException; | 按列名获取Time型数据。                     |
| Timestamp getTimestamp(int columnIndex)                | 按列标获取TimeStamp型数据。                |
| Timestamp getTimestamp(String columnLabel)             | 按列名获取TimeStamp型数据。                |
| Date getDate(int columnIndex)                          | 按列标获取Date型数据                       |
| Date getDate(String columnLabel)                       | 按列名获取Date型数据。                     |

#### 获取流式结果集

流式结果集是指对查询结果进行流式获取，能够在结果集数据量较大时显著提升数据获取效率。流式结果集开启条件为stmt设置fetchSize为Integer.MIN_VALUE，resultset type为ResultSet.TYPE_FORWARD_ONLY，resultsetConcurrency为ResultSet.CONCUR_READ_ONLY。

示例：

```java
Properties info = new Properties();
info.setProperty("user", "username");
info.setProperty("password", "password");
Connection conn= DriverManager.getConnection(url, info);

Statement stmt = conn.createStatement(ResultSet.TYPE_FORWARD_ONLY, ResultSet.CONCUR_READ_ONLY);

stmt.setFetchSize(Integer.MIN_VALUE);
```

也可通过URL配置connection，此时该connection下创建的stmt默认执行流式获取，示例：

```java
String url = "jdbc:yasdb://192.168.1.2:1688/yasdb?defaultRowFetchSize=-2147483648&netTimeoutForStreamingResults=120";
Properties info = new Properties();
info.setProperty("user", "username");
info.setProperty("password", "password");
Connection conn= DriverManager.getConnection(url, info);

Statement stmt = conn.createStatement();
```

| 参数| 描述|
| ----------------------------- | ------------------------------------------------------------ |
| defaultRowFetchSize           | stmt执行查询时的单次获取行数。默认值为10（表示使用驱动默认值），设置为正整数可控制每次从数据库获取的行数。 |
| netTimeoutForStreamingResults | 开始流式结果集后，服务端发送时阻塞超时时间，单位为秒。默认值为600秒，设置为正整数，超时后服务端会断开连接。 |

> **Note**:
>
> 流式结果集适用场景：
> * 优点：流式结果集在结果集数据量比较大时，能快速地把数据获取过来，显著减少内存占用。
> * 限制：
>   * 只能向前滚动（TYPE_FORWARD_ONLY），不支持滚动；不支持获取行号；不支持双向遍历。
>   * 流式结果集在数据获取结束前不能有二次交互，如LOB和cursor等需要二次交互的数据类型不适合使用流式结果集。
> * 注意事项：需要在网络断开或结果集关闭前完成数据读取，否则可能导致连接异常。

完整的示例代码示例如下：

```java
package quickstart;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.Statement;
import java.sql.SQLException;
import java.util.Properties;

public class ResultSetDemo {

    //数据库连接参数，替换为自己的配置
    private static final String URL = "jdbc:yasdb://192.168.1.2:1688/yasdb";
    private static final String USER = "sales";
    private static final String PASSWORD = "sales";

    public static void main(String[] args) {

        //声明资源对象：Connection/Statement/ResultSet
        Connection conn = null;
        Statement stmt = null;
        ResultSet rs = null;

        try {
            //获取数据库连接
            Properties info = new Properties();
            info.setProperty("user", USER);
            info.setProperty("password", PASSWORD);
            conn = DriverManager.getConnection(URL, info);

            //创建Statement对象，通过Connection createStatement()方法创建
            stmt = conn.createStatement();

            //示例：执行插入
            String insertSql = "INSERT INTO customer(id, name) VALUES (2, 'Tom')";
            //执行插入，返回受影响行数
            int affectedRows = stmt.executeUpdate(insertSql);
            System.out.println("Data inserted successfully, number of affected rows: " + affectedRows);
            
            //示例：执行查询
            String querySql = "SELECT id, name FROM customer WHERE id = 2";
            
            //执行查询，返回结果集
            rs = stmt.executeQuery(querySql);
            
            //处理结果集
            while (rs.next()) {
                int id = rs.getInt("id");
                String name = rs.getString("name");
                System.out.println("Query Result: ID=" + id + "，Name=" + name);
            }

        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            //关闭资源（顺序：ResultSet → Statement → Connection）
            try {
                if (rs != null) rs.close();
                if (stmt != null) stmt.close();
                if (conn != null) conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```

#### 设置只读结果集数据敏感性

当resultSetConcurrency为ResultSet.CONCUR_READ_ONLY且resultSetType为ResultSet.TYPE_SCROLL_SENSITIVE时，理论上在数据修改后重新滚动到修改所在行，可以看到修改后的结果。但基于内部实现逻辑，视图等功能并不适用。打开开关后，即使resultSetType为ResultSet.TYPE_SCROLL_SENSITIVE，对可更新结果集所做的编辑也不进行显示，即结果集对数据修改不敏感。

开启方式为在Connection的URL中修改readOnlyResultSetInsensitive=TRUE参数，该参数默认为FALSE，表示对所有的只读结果集数据敏感。

示例：

```java
String url = "jdbc:yasdb://192.168.1.2:1688/yashan?readOnlyResultSetInsensitive=true";
Properties info = new Properties();
info.setProperty("user", "username");
info.setProperty("password", "password");
Connection conn = DriverManager.getConnection(url, info);

Statement stmt = conn.createStatement();
```

| 参数| 描述|
| ----------------------------- | ------------------------------------------------------------ |
| readOnlyResultSetInsensitive  | 只读结果集是否忽略数据修改，TRUE表示忽略数据修改，FALSE表示感知数据修改，默认FALSE。|

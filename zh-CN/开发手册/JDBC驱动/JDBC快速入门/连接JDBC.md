YashanDB JDBC中提供了两种建立数据库连接的方法：通过DriverManager和DataSource。

DriverManager核心是通过DriverManager静态方法获取连接，每次调用都会新建一个物理连接。

> **Note**:
>
> 高并发下频繁创建或销毁连接，数据库性能开销大。
> 连接参数硬编码，维护成本高。
> 需手动保证连接关闭，否则易导致连接泄露。

数据源（DataSource）连接是主流实现方式，核心是连接复用。

> **Note**:
>
> 连接池复用连接，避免频繁创建或销毁的性能损耗。自动管理连接生命周期，空闲回收、超时关闭。支持外置配置，可把参数写在properties文件中，无需改代码。可监控连接状态，如活跃连接数、空闲连接数等。

- DriverManager连接：适用快速入门，快速测试，学习JDBC基础时使用。


- YashanDB数据源连接：适用生产环境、高并发应用等所有正式场景。

### DriverManager连接

使用DriverManager类时，按照以下方法设置参数即可建立与YashanDB数据库的连接：

```java
String url = "jdbc:yasdb://ipaddress:port/databasename";
Properties info = new Properties();
info.setProperty("user", "username");
info.setProperty("password", "******");
Class.forName("com.yashandb.jdbc.Driver");
Connection connection = DriverManager.getConnection(url, info);
```

使用DriverManager之前需要使用Class.forName加载类，然后使用YashanDB数据库的IP地址及端口号进行数据库连接，将字段填入相应的IP地址、端口号和Schema名称。

完整的连接代码示例如下：

```java
package quickstart;

// 文件存放路径：src/quickstart/HelloWorld.java（需创建quickstart目录）

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.util.Properties;

public class HelloWorld {
   public static void main(String[] args) throws SQLException {

	   String url = null;
       Connection conn = null;
       Properties info = null;

       try {
           //创建数据库连接
           url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
           info = new Properties();
           info.setProperty("user", "sales");
           info.setProperty("password", "sales");
           //加载YashanDB数据库驱动
           Class.forName("com.yashandb.jdbc.Driver");
           conn = DriverManager.getConnection(url, info);
           System.out.println("Hello World");
       } catch (Throwable e) {
           e.printStackTrace();
       } finally {
           conn.close();
      }
   }
}
```

以Windows环境为例编译：

```java
$ javac HelloWorld.java
```

执行：

```java
$ java quickstart.HelloWorld
Hello World
```

> **Note**:
>
> 对于JDBC 4.0及以上版本（Java 6及以上），无需显式调用`Class.forName()`加载驱动，JDBC驱动会通过SPI机制自动加载。上述示例中保留该调用是为了兼容性考虑。

除标准连接参数外，JDBC驱动程序还提供了部分用于指定其工作方式的连接参数，此类参数既可以在连接URL中设置，也可保存在Properties对象中，详细请参考[JDBC进阶配置](../JDBC进阶配置/00JDBC进阶配置.md)。

```java
package quickstart;

// 文件存放路径：src/quickstart/HelloWorld.java（需创建quickstart目录）

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.util.Properties;

public class HelloWorldProperties {
	
	public static void main(String[] args) throws SQLException {
		   
		 String url = null;
         Properties info = null;
         Connection conn = null;
      
	       try {
	           //创建数据库连接
	           url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
	           info = new Properties();
	           info.put ("user", "sales");
	           info.put ("password", "sales");
	           conn = DriverManager.getConnection(url, info);
	           System.out.println("Hello World");
	       } catch (Exception e) {
	           e.printStackTrace();
	       } finally {
	            conn.close();
	       }
	   }

}
```

以Windows环境为例编译：

```shell
$ javac HelloWorldProperties.java
```

执行：

```shell
$ java quickstart.HelloWorldProperties
Hello World
```
<span id="Connection" name="Connection"></span>

### YashanDB数据源连接 

DataSource接口是在JDBC3.0的规范中定义的另外一种获取数据源连接的方法。使用DataSource进行数据库连接可以提高应用程序的可移植性，因为应用程序使用一个逻辑名称建立与数据库的连接，该逻辑名通过使用JNDI (Java Naming and Directory Interface) 命名服务映射到真正的DataSource对象，该DataSource对象表示了一个真正的数据库连接。如果数据源的信息发生变化，只需修改该DataSource对象的属性，而不需要修改应用程序。

DataSource对象是推荐使用的方法，它使得应用程序更容易移植和维护，而且应用程序可以透明地使用连接池和分布事务的功能。

YasDataSource是YashanDB提供的一个实现了标准javax.sql.DataSource接口的JDBC数据源类。它用于创建与YashanDB数据库的连接，并支持通过JNDI进行注册和管理。

该类提供了灵活的配置方式，允许开发者通过设置各种属性来定义数据库连接参数，例如服务器地址、端口号、用户名、密码等。同时，也支持SSL安全认证以及自定义连接属性等功能。

示例：基本使用方法

```java
//初始化YashanDB数据源
YasDataSource ds = new YasDataSource();

//设置数据库服务主机名/IP地址
ds.setServerName("192.168.1.1");

//设置监听端口号，例如1688
ds.setPortNumber(1688);

//设置数据库名称/SID
ds.setDatabaseName("testdb");

//设置登录用户名和密码
ds.setUser("your_username");
ds.setPassword("your_password");

//（可选）设置SSL根证书路径
ds.setSslRootCer("/path/to/root.crt");

//获取数据库连接
Connection conn = ds.getConnection();

//或者指定不同的用户名和密码进行连接
Connection conn = ds.getConnection("another_user", "another_password");
```

常用配置项说明如下所示：

#### 配置项详解 

| 方法| 描述|
| ------------------------------------- | ------------------------------------------------------------ |
| void setServerName(String sn)         | 设置数据库所在服务器的IP或域名                               |
| String getServerName()                | 获取数据库所在服务器的IP或域名                               |
| void setPortNumber(int port)          | 设置监听端口，默认值由常量定义（通常为1688），这是YashanDB默认的监听端口号。                 |
| int getPortNumber()                   | 获取监听端口                                                 |
| void setDatabaseName(String dbName)   | 设置要连接的目标数据库名称                                   |
| String getDatabaseName()              | 获取要连接的目标数据库名称                                   |
| void setUser(String userID)           | 设置登录数据库使用的用户名                                   |
| String getUser()                      | 获取登录数据库使用的用户名                                   |
| void setPassword(String password)     | 设置登录数据库使用的密码                                     |
| void setSslRootCer(String sslRootCer) | 设置启用SSL通信时所需的CA文件路径                            |
| String getSslRootCer()                | 获取SSL通信时的CA文件路径                                    |
| void setLoginTimeout(int seconds)     | 设置建立连接的最大等待秒数                                   |
| int getLoginTimeout()                 | 获取建立连接的最大等待秒数                                   |

完整的代码示例如下：

```java
//YashanDB 数据源示例代码：
package quickstart;

import com.yashandb.jdbc.YasDataSource;
import java.sql.Connection;
import java.sql.Statement;
import java.sql.ResultSet;

public class DataSourceDemo {
    public static void main(String[] args) {
        try {
           YasDataSource ds = new YasDataSource();

           //设置连接参数
           ds.setServerName("192.168.1.1");    // Database server address
           ds.setPortNumber(1688);          // Port number
           ds.setDatabaseName("yasdb");     // Database name
           ds.setUser("sales");             // Username
           ds.setPassword("sales");    // Password

           //获取连接
           Connection conn = ds.getConnection();

           //执行简单查询
           Statement stmt = conn.createStatement();
           ResultSet resultSet = stmt.executeQuery("select 1 from dual");
           while(resultSet.next()) {
        	   System.out.println("YashanDB DataSource Demo");
           }

           //关闭资源
           resultSet.close();
           stmt.close();
           conn.close();
            
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

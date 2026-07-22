YashanDB JDBC provides two methods to establish a database connection: through DriverManager and DataSource. 

The core of DriverManager is to obtain a connection through its static methods, and a new physical connection is created with each call.

> **Note**:
>
> Frequent creation or destruction of connections under high concurrency leads to significant performance overhead on the database.
> Hard-coded connection parameters result in high maintenance costs.
> Connections must be manually closed; otherwise, connection leaks may easily occur.

The DataSource connection is the mainstream implementation, with connection reuse as its core feature.

> **Note**:
>
> The connection pool reuses connections, avoiding performance losses caused by frequent creation or destruction. Automatically manages the connection lifecycle, including idle recycling and timeout closure. Supports external configuration, parameters can be written in a properties file without modifying the code. Allows monitoring of connection status, such as the number of active connections and idle connections.

- DriverManager Connection: Suitable for quick start, rapid testing, and learning the basics of JDBC.


- YashanDB DataSource Connection: Suitable for all production scenarios such as production environments and high-concurrency applications.

### DriverManager Connection

When using the DriverManager class, you can establish a connection to the YashanDB database by setting parameters according to the following methods:

```java
String url = "jdbc:yasdb://ipaddress:port/databasename";
Properties info = new Properties();
info.setProperty("user", "username");
info.setProperty("password", "******");
Class.forName("com.yashandb.jdbc.Driver");
Connection connection = DriverManager.getConnection(url, info);
```

Before using DriverManager, you need to load the class with Class.forName, then use the IP address and port number of the YashanDB database to establish a database connection, and fill in the corresponding IP address, port number and Schema name into the fields.

The complete connection code example is as follows:

```java
package quickstart;

// File location: src/quickstart/HelloWorld.java (need to create quickstart directory)

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
           //Create database connection
           url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
           info = new Properties();
           info.setProperty("user", "sales");
           info.setProperty("password", "sales");
           //Load YashanDB driver
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

Compile in a Windows environment:

```java
$ javac HelloWorld.java
```

Execute:

```java
$ java quickstart.HelloWorld
Hello World
```

> **Note**:
>
> For JDBC 4.0 and above (Java 6 and above), there is no need to explicitly call `Class.forName()` to load the driver; the JDBC driver will be automatically loaded via the SPI mechanism. The above example retains this call for compatibility considerations.

In addition to the standard connection parameters, JDBC drivers provide some connection parameters that specify how the driver works. These parameters can be set in the connection URL or in a Properties object. For detailed information, please refer to [JDBC Advanced Configuration](../YashanDB JDBC Advanced Configuration/00YashanDB JDBC Advanced Configuration).

```java
package quickstart;

// File location: src/quickstart/HelloWorld.java (need to create quickstart directory)

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
	           //Create database connection
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

Compile in a Windows environment:

```shell
$ javac HelloWorldProperties.java
```

Execute:

```shell
$ java quickstart.HelloWorldProperties
Hello World
```
<span id="Connection" name="Connection"></span>

### YashanDB DataSource Connection

The DataSource interface is another method defined in the JDBC 3.0 specification for obtaining connections to a data source. Using DataSource for database connections can improve the portability of applications, because an application establishes a connection to the database using a logical name, which is mapped to the actual DataSource object through the JNDI (Java Naming and Directory Interface) naming service — this DataSource object represents a real database connection. If the data source information changes, only the properties of the DataSource object need to be modified, without altering the application itself.

The DataSource object is the recommended approach, as it makes applications more portable and maintainable, and enables applications to transparently use connection pooling and distributed transaction capabilities.

YasDataSource is a JDBC data source class provided by YashanDB that implements the standard javax.sql.DataSource interface. It is used to create connections to the YashanDB database and supports registration and management via JNDI.

This class offers flexible configuration methods, allowing developers to define database connection parameters by setting various properties (e.g., server address, port number, username, password, etc.). It also supports SSL secure authentication and custom connection properties, among other features.

Example: Basic usage

```java
//Initialize YashanDB data source
YasDataSource ds = new YasDataSource();

//Set the database service hostname/IP address
ds.setServerName("192.168.1.1");

//Set the listening port number, e.g., 1688
ds.setPortNumber(1688);

//Set the database name/SID
ds.setDatabaseName("testdb");

//Set the login username and password
ds.setUser("your_username");
ds.setPassword("your_password");

// (Optional) Set the SSL root certificate path
ds.setSslRootCer("/path/to/root.crt");

//Get database connection:
Connection conn = ds.getConnection();

//Or connect with a different specified username and password
Connection conn = ds.getConnection("another_user", "another_password");
```

Descriptions of the commonly used configuration items are as follows:

#### Configuration items Explanations

|Method                       |Description                                         |
| ------------------------------------- | ------------------------------------------------------------ |
| void setServerName(String sn)         | Set the IP address or domain name of the server where the database resides |
| String getServerName()                | Get the IP address or domain name of the server where the database resides |
| void setPortNumber(int port)          | Set the listening port; the default value is defined by a constant (usually 1688), which is YashanDB's default listening port number. |
| int getPortNumber()                   | Get the listening port                                       |
| void setDatabaseName(String dbName)   | Set the name of the target database to connect to            |
| String getDatabaseName()              | Get the name of the target database to connect to            |
| void setUser(String userID)           | Set the username used to log in to the database              |
| String getUser()                      | Get the username used to log in to the database              |
| void setPassword(String password)     | Set the password used to log in to the database              |
| void setSslRootCer(String sslRootCer) | Set the path of the CA file required for enabling SSL communication |
| String getSslRootCer()                | Get the path of the CA file used for SSL communication       |
| void setLoginTimeout(int seconds)     | Set the maximum wait time (in seconds) for establishing a connection |
| int getLoginTimeout()                 | Get the maximum wait time (in seconds) for establishing a connection |

The complete code example is as follows:

```java
//YashanDB Data source sample code:
package quickstart;

import com.yashandb.jdbc.YasDataSource;
import java.sql.Connection;
import java.sql.Statement;
import java.sql.ResultSet;

public class DataSourceDemo {
    public static void main(String[] args) {
        try {
           YasDataSource ds = new YasDataSource();

           //Set connection parameters
           ds.setServerName("192.168.1.1");    // Database server address
           ds.setPortNumber(1688);          // Port number
           ds.setDatabaseName("yasdb");     // Database name
           ds.setUser("sales");             // Username
           ds.setPassword("sales");    // Password

           //Get connection
           Connection conn = ds.getConnection();

           //Execute simple query
           Statement stmt = conn.createStatement();
           ResultSet resultSet = stmt.executeQuery("select 1 from dual");
           while(resultSet.next()) {
        	   System.out.println("YashanDB DataSource Demo");
           }

           //Close resources
           resultSet.close();
           stmt.close();
           conn.close();
            
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

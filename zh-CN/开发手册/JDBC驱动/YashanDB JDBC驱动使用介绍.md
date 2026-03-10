## 连接数据库

### 基本连接配置

YashanDB JDBC驱动提供如下两种建立数据库连接的方法。

#### DriverManager连接

可通过如下三个DriverManager方法建立数据库连接：

*   DriverManager.getConnection(String url);
*   DriverManager.getConnection(String url, Properties info);
*   DriverManager.getConnection(String url, String user, String password);

#### YasDataSource连接

通过YashanDB提供的YasDataSource类建立数据库连接：

*   YasDataSource ads = new YasDataSource();

#### 参数说明

|  参数| 描述|
| -------- | ------------------------------------------------------------ |
| url      | 数据库连接描述符，格式为jdbc:yasdb://host:port/database_name<br>\*   host：服务器域名或IP地址，需配置为单机/集群实例服务器地址或分布式CN服务器地址，不填时表示连接localhost。<br>\*   port：数据库服务端口，例如1688。<br>\*   database_name：数据库名称，必填项。该参数仅用于兼容，无实际含义且不校验参数值。 |
| user     | 数据库用户名。                                               |
| password | 数据库用户密码。                                             |
| info     | 数据库连接属性。                                             |

示例

```java
//DriverManager方法
public static Connection getConnection(String username, String passwd) {
        //驱动类。
        String driver = "com.yashandb.jdbc.Driver";
        //数据库连接描述符。
        String sourceURL = "jdbc:yasdb://10.10.10.2:1688/yasdb";
        Connection conn = null;
         
        try {
            //加载驱动。
            Class.forName(driver);
        } catch( Exception e ) {
            e.printStackTrace();
            return null;
        }
         
        try {
             //创建连接。
            conn = DriverManager.getConnection(sourceURL, username, passwd);
            System.out.println("Connection succeed!");
        } catch(Exception e) {
            e.printStackTrace();
            return null;
        }
         
        return conn;
    }
 
//YasDataSource方法
public static Connection getConnDS() {
    Connection connect = null;
    try {
      YasDataSource ads = new YasDataSource();
      ads.setURL(url);
      connect = ads.getConnection(user, password);
    } catch (Exception e) {
      e.printStackTrace();
    }
    return connect;
  }
```

### 特殊连接格式

上述参数说明中的URL为基本连接配置格式，YashanDB JDBC驱动还支持一些特殊连接格式的配置。

#### mysql兼容

JDBC提供了mysql兼容模式的参数，可以把语法模式设置为mysql或yashan。

相关参数如下：

|  参数| 类型| 描述|
| ------------------------- | ------------------------------------------------------------ |---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| compatVector | string | 兼容模式设置，只有在服务端为mysql模式时才需要设置，只能设置为mysql和yashan。<br/>设置为mysql，驱动会自动把语法模式设置为mysql兼容模式，传入的用户名会区分大小写，且元数据DatabaseMetaData的getDatabaseProductName接口的返回值会变成mysql。<br/>设置为yashan，驱动会把语法模式设置为yashan。 |

URL连接配置示例：

```java
jdbc:yasdb://192.168.1.2:1688/yashan?compatVector=mysql
```

#### 超时时间配置

YashanDB JDBC驱动建立了超时处理机制，并允许通过配置超时时间让程序在指定期限达到时进行友好退出。

可供配置的超时时间及说明在下表中列出：

|  超时时间| 说明|
| --------------- | ------------------------------------------------------------ |
| connectTimeout  | 建立连接时，创建socket连接的超时时间，单位为s。不配置时默认为10s。通过info属性或者URL配置。 |
| socketTimeout   | 获取connection后，TCP通信过程中，客户端等待服务端返回数据的超时时间，单位为s。不配置时默认为0s，一直等待。通过info属性或者URL配置。 |
| loginTimeout    | 创建socket成功后，进行登录认证时，客户端等待服务端返回数据的超时时间，单位为s。不配置时默认为300s。通过info属性或者URL配置。 |
| SQL语句执行超时时间 | 通过statement接口setQueryTimeout配置。不配置时默认无超时时间，一直等待。 |

connectTimeout、socketTimeout以及loginTimeout通过URL进行配置时的格式示例如下：

```java
jdbc:yasdb://192.168.1.2:1688/yashan?connectTimeout=60&socketTimeout=120&loginTimeout=60
```

#### 连接模式配置

YashanDB JDBC驱动通过以下参数控制连接模式和数据库产品名：

|  参数| 说明|
| --------- | ---------------------- |
| erverMode    | 指定连接模式，可选值如下：<br />* shared：默认值，表示连接共享服务器模式，即客户端连接允许采用共享线程池处理（实际是否采用共享线程池，全凭YashanDB服务端配置参数[MAX_REACTOR_CHANNELS](../../参考手册/配置参数.html#max_reactor_channels)决定）。<br />* dedicated：表示连接专用服务器模式，即每个客户端连接请求必须单独分配一个线程（不受YashanDB服务端相关配置影响）。 |
| productName | （隐藏参数）指定数据库产品名称标识，省略时默认识别为YashanDB，若指定则可绕过自动识别强制按指定值生效。 |

URL连接配置示例：

```java
jdbc:yasdb://192.168.1.2:1688/yashan?connecTimeout=60&socketTimeout=120&loginTimeout=60&serverMode=dedicated
```

<span id="IPs" name="IPs" class="yaslink"></span>

#### 多IP/PORT连接配置

在高可用主备和负载均衡场景下，可以配置多个IP/PORT或多组IP/PORT进行连接。

多个IP/PORT间用`,`分隔，连接时根据serverType配置对相应节点进行连接。

多组IP/PORT间用`;`分隔，连接时先在组内根据serverType配置对相应节点进行连接，当组内所有连接均失败后按顺序优先级（越靠前优先级越高）访问下一组。

URL参数格式为：

```java
jdbc:yasdb:serverType://host1:port1,host2:port2;host3:port3,host4:port4/databasename

// serverType=primary|standby时，可以指定poolTimeout
jdbc:yasdb:serverType://host1:port1,host2:port2;host3:port3,host4:port4/databasename?poolTimeout=value
```

配置项介绍如下：

- serverType：可选参数，服务类型，可选项包括[primary|standby|loadBalance|primaryLoadBalance|standbyLoadBalance]。若不指定serverType，在输入多IP时默认采用primary。

    - primary：表示主节点顺序优先级连接，JDBC将按照顺序自动识别出主节点并连接。
  
    - standby：表示备节点顺序优先级连接，JDBC将自动识别出备节点并连接。
  
    - loadBalance：表示负载均衡连接，JDBC将自动识别出连接数最少的节点并连接。如需使用该功能，用户必须具备[SELECT_CATALOG_ROLE](../../产品安全/数据访问控制/特权与角色管理（yashan模式）/角色.html#buildinroles)权限。
  
    - primaryLoadBalance：表示主节点负载均衡连接，JDBC将自动识别出连接数最少的主节点并连接。如果主节点的连接数相同，则优先连接URL连接配置中靠前的IP/PORT。如需使用该功能，用户必须具备[SELECT_CATALOG_ROLE](../../产品安全/数据访问控制/特权与角色管理（yashan模式）/角色.html#buildinroles)权限。

    - standbyLoadBalance：表示备节点负载均衡连接，JDBC将自动识别出连接数最少的备节点并连接。如果备节点的连接数相同，则优先连接URL连接配置中靠前的IP/PORT。如需使用该功能，用户必须具备[SELECT_CATALOG_ROLE](../../产品安全/数据访问控制/特权与角色管理（yashan模式）/角色.html#buildinroles)权限。

- poolTimeout：可选参数，采用primary或standby服务类型时的连接超时时间，单位为秒。若不指定poolTimeout，默认值为300。

URL连接配置示例：

```java
// 示例1：primary + 多个IP/PORT + poolTimeout
jdbc:yasdb:primary://192.168.1.2:1688,192.168.1.3:1688,192.168.1.4:1688/yashan?poolTimeout=180

// 示例2：standby + 多组IP/PORT + poolTimeout
jdbc:yasdb:standby://192.168.1.2:1688,192.168.1.3:1688;192.168.1.4:1688,1688,192.168.1.5:1688/yashan?poolTimeout=180

// 示例3：loadBalance + 多个IP/PORT 
jdbc:yasdb:loadBalance://192.168.1.2:1688,192.168.1.3:1688,192.168.1.4:1688/yashan

// 示例4：primaryLoadBalance + 多组IP/PORT
jdbc:yasdb:primaryLoadBalance://192.168.1.2:1688,192.168.1.3:1688;192.168.1.4:1688,1688,192.168.1.5:1688/yashan

// 示例5：standbyLoadBalance + 多个IP/PORT
jdbc:yasdb:standbyLoadBalance://192.168.1.2:1688,192.168.1.3:1688,192.168.1.4:1688/yashan
```

#### 安全连接配置

JDBC的安全连接配置包括SSL加密通信、TLCP加密通信、SM3密码哈希算法、SM4密码加密算法以及UKEY安全登录，此类安全配置的开关均在服务端，若服务端开启了相应的安全配置开关，JDBC需要进行相应的设置，否则无法获取连接进行数据库操作。

##### SSL加密通信

若服务端开启了SSL加密通信，JDBC必须配置以下参数：

|  配置参数名| 参数描述|
|-------------------| ---------------------- |
| sslRootCer    | 为SSL加密通信根证书的标准路径，可以为绝对路径或相对路径。 |

URL连接配置示例：

```java
jdbc:yasdb://192.168.1.2:1688/yashan?sslRootCer=./ca.crt
```

##### TLCP加密通信

若服务端开启了TLCP加密通信，需要从安装包里获取相应的动态库并存放至指定路径：

|  动态库| 文件名| 存放至以下路径|
|--------------|-----| ---------------------- |
| yashandb-gmssl-jni动态库  | \* Windows系统：yas_gmssl_jni.dll<br/>\* Linux系统：libyas_gmssl_jni.so | 存放至java.library.path所包含的目录下。<br/>默认情况下，java.library.path包含工程根目录、Path目录、OS的系统目录等，也可以在项目启动命令里加上JVM参数 -Djava.library.path=XXX显式设置该路径。 |
| GmSSL动态库  | \* Windows系统：gmssl.dll<br/>\* Linux系统：libgmssl.so  | \* Windows系统：存放至Path路径下。<br/>\* Linux系统：存放至LD_LIBRARY_PATH目录下。 |

除了动态库，JDBC还必须配置以下参数：

|  配置参数名| 参数描述|
|-------------------| ---------------------- |
| tlcpCacertFile    | TLCP加密通信根证书的标准路径，可以为绝对路径或相对路径。 |
| tlcpCertFile      | TLCP加密通信客户端证书的标准路径，可以为绝对路径或相对路径。 |
| tlcpClientKeyFile | TLCP加密通信客户端key文件的标准路径，可以为绝对路径或相对路径。 |
| tlcpClientKeyPass | TLCP加密通信客户端key的密码。 |

URL连接配置示例：

```java
jdbc:yasdb://192.168.1.2:1688/yashan?sslRootCer=D:\gmssl_ca\rootcacert.pem&tlcpCertFile=D:\gmssl_ca\clientcert.pem&tlcpClientKeyFile=D:\gmssl_ca\clientkey.pem&tlcpClientKeyPass=123456
```

##### SM3密码哈希算法

若服务端开启了SM3密码哈希算法，需要从安装包里获取相应的动态库并存放至指定路径：

|  动态库| 文件名| 存放至以下路径|
|--------------|-----| ---------------------- |
| YASJDBC动态库  | \* Windows系统：YASJDBC.dll<br/>\* Linux系统：YASJDBC.so | 存放至java.library.path所包含的目录下。<br/>默认情况下，java.library.path包含工程根目录、Path目录、OS的系统目录等，也可以在项目启动命令里加上JVM参数 -Djava.library.path=XXX显式设置该路径。 |
| GmSSL动态库  | \* Windows系统：gmssl.dll<br/>\* Linux系统：libgmssl.so  | \* Windows系统：存放至Path路径下。<br/>\* Linux系统：存放至LD_LIBRARY_PATH目录下。 |

##### SM4密码加密算法

若服务端开启了SM4密码加密算法，需要从安装包里获取相应的动态库并存放至指定路径：

|  动态库| 文件名| 存放至以下路径|
|--------------|-----| ---------------------- |
| YASJDBC动态库  | \* Windows系统：YASJDBC.dll<br/>\* Linux系统：YASJDBC.so | 存放至java.library.path所包含的目录下。<br/>默认情况下，java.library.path包含工程根目录、Path目录、OS的系统目录等，也可以在项目启动命令里加上JVM参数 -Djava.library.path=XXX显式设置该路径。 |
| GmSSL动态库  | \* Windows系统：gmssl.dll<br/>\* Linux系统：libgmssl.so  | \* Windows系统：存放至Path路径下。<br/>\* Linux系统：存放至LD_LIBRARY_PATH目录下。 |

<span id="UKEY" name="UKEY" class="yaslink"></span>

##### UKEY安全登录
若服务端开启了UKEY安全登录，需要从安装包里获取相应的动态库并存放至指定路径：

|  动态库| 文件名| 存放至以下路径|
|--------------|-----| ---------------------- |
| YASJDBC动态库  | \* Windows系统：YASJDBC.dll<br/>\* Linux系统：YASJDBC.so | 存放至java.library.path所包含的目录下。<br/>默认情况下，java.library.path包含工程根目录、Path目录、OS的系统目录等，也可以在项目启动命令里加上JVM参数 -Djava.library.path=XXX显式设置该路径。 |
| libgm3000动态库  | \* Windows系统：mtoken_gm3000.dll<br/>\* Linux系统：libgm3000.1.0.so  | \* Windows系统：存放至Path路径下。<br/>\* Linux系统：存放至LD_LIBRARY_PATH目录下。 |

除了动态库，JDBC还必须配置以下参数：

|  参数名| 合法值| 参数描述|
|--------------|---------------------|------------------------------|
| UKeyName    | \* DBA：如果登录的用户角色是DBA，则要求UKEY的设备名为DBA<br />\* SECURITY_ADMIN：如果登录的用户角色是安全管理员，则要求UKEY的设备名为SECURITY_ADMIN<br />\* AUDIT_ADMIN ：如果登录的用户角色是审计员，则要求UKEY的设备名为AUDIT_ADMIN | 该参数表示要认证的UKEY设备名，设备名要求和用户的角色名保持对应。 |
| UKeyPin     | 小于64位的密码值            | 该参数指定UKEY的用户密码。           |

URL连接配置示例：

```java
jdbc:yasdb://192.168.1.2:1688/yashan?UKeyName=DBA&UKeyPin=Yashan123
```

#### 透明应用故障转移TAF配置

TAF相关配置与使用，请查阅[透明应用程序故障转移](JDBC高级功能说明/透明应用程序故障转移)。

#### 心跳连接的配置

开启心跳连接能够使得程序更快的检测到正在使用的连接已经异常，从而跳出当前等待返回数据的状态，从而触发后续的操作，例如故障恢复。

心跳连接相关参数如下：

|  参数| 类型| 描述|
| ------------------------- | ------------------------------------------------------------ |---------------------------------------------------|
| heartbeatSwitch | string | 该参数表示心跳连接是否开启，由ON和OFF两个可选值，默认为ON。 |
| heartbeatSocketTimeout | int | 心跳连接的保活超时时间（单位：秒），默认为60秒。                            |
| heartbeatSchedulePeriod | int | 心跳连接的保活间隔时间（单位：秒），默认为20秒。                            |

URL连接配置示例：

```java
jdbc:yasdb://192.168.1.2:1688/yashan?heartbeatSwitch=on&heartbeatSocketTimeout=60&heartbeatSchedulePeriod=20
```

#### 日期时间类型转换

开启日期时间类型转换，可以将data日期类型列部分接口返回时间Timestamp类型列属性。影响接口如下：

- ResultSet.getString(int columnIndex)将返回Timestamp形式的string。
- ResultSet.getObject(int columnIndex)将返回Timestamp类。
- ResultSetMetaData.getColumnClassName(int column)将返回Timestamp.class。

相关参数如下：

|  参数| 类型| 描述|
| ------------------------- | ------------------------------------------------------------ |--------------------------------------------|
| mapDateToTimestamp | string | 该参数表示日期转时间类型是否开启，TRUE表示开启，FALSE表示不开启，默认为FALSE。|

URL连接配置示例：

```java
jdbc:yasdb://192.168.1.2:1688/yashan?mapDateToTimestamp=true
```

#### 支持多SQL

开启支持多SQL后，可以同时传入多条SQL语句，SQL语句间用分号（`;`）隔开，最后1条SQL语句末尾的分号可选。传入的SQL语句将按照先后顺序依次执行，执行出错时终止并返回报错，执行成功后JDBC接口始终只返回第1条SQL语句的执行结果。

目前仅支持执行不带参数的SQL语句，且执行中不允许包含autoGeneratedKey。

支持多SQL的相关接口如下：

- Statement.executeUpdate(String sql)
- Statement.executeQuery(String sql)
- Statement.addBatch(String sql)
- Statement.execute(String sql)

相关参数如下：

|  参数| 类型| 描述|
| ------------------------- | ------------------------------------------------------------ |----------------------------------------------|
| allowMultiStmt | string | 是否开启支持多SQL，TRUE表示开启，FALSE表示不开启，默认为FALSE。 |

URL连接配置示例：

```java
jdbc:yasdb://192.168.1.2:1688/yashan?allowMultiStmt=true
```

## 执行SQL

### 执行普通SQL

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

### 执行绑定参数的SQL

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
>使用pstmt.setString接口传入字符串时，需注意，由于YashanDB在23.1及之后版本新增了[变量窥视](../PL参考手册/参数/绑定参数)功能，系统对于语句待绑定参数的预推导类型逻辑发生了变化。若在v23.1及之后版本中执行以下用例，WHERE条件将无法成功匹配，而v23.1之前版本则可以正常匹配，二者查询结果会有差异。

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

对于绑定参数执行场景，JDBC客户端默认会先向服务端发送prepare命令，然后发送绑定参数执行。可以通过支持直接执行绑定参数配置，将prepareStatement的SQL语句和要绑定的参数一次性发给服务端，减少交互次数。开启方式：

|  配置/接口| 说明| 示例|
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

## 获取结果集

### 设置结果集类型

不同类型的结果集有各自的应用场景，应用程序需要根据实际情况选择相应的结果集类型。在执行SQL语句的过程中，都需要先创建相应的语句对象。而部分创建语句对象的方法提供了设置结果集类型的功能。涉及的Connection的方法如下：

```java
//创建一个Statement对象。
createStatement();
 
//创建一个PreparedStatement对象。
prepareStatement(String sql);
 
//创建一个CallableStatement对象。
prepareCall(String sql);
```

结果集类型说明如下所示：

|  参数| 描述|
| -------------------- | ------------------------------------------------------------ |
| resultSetType        | 表示结果集的类型，具体有三种类型：<br>\*   ResultSet.TYPE_FORWARD_ONLY：ResultSet只能向前移动，是缺省值。<br>\*   ResultSet.TYPE_SCROLL_SENSITIVE：在修改后重新滚动到修改所在行，可以看到修改后的结果。<br>\*   ResultSet.TYPE_SCROLL_INSENSITIVE：对可修改例程所做的编辑不进行显示。 |
| resultSetConcurrency | 表示结果集的并发，具体有两种类型： <br>\*  ResultSet.CONCUR_READ_ONLY：如果不从结果集中的数据建立一个新的更新语句，不能对结果集中的数据进行更新。<br>\*  ResultSet.CONCUR_UPDATEABLE：可改变的结果集。对于可滚动的结果集，可对结果集进行适当的改变。 |

### 在结果集中定位

ResultSet对象具有指向其当前数据行的光标。最初，光标被置于第一行之前。next方法将光标移动到下一行；因为该方法在ResultSet对象没有下一行时返回false，所以可以在while循环中使用它来迭代结果集。但对于可滚动的结果集，JDBC驱动程序提供更多的定位方法，如下所示：

#### 在结果集中定位的方法

|  方法| 描述|
| ------------- | --------------------------------- |
| next()        | 把ResultSet向下移动一行。         |
| previous()    | 把ResultSet向上移动一行。         |
| beforeFirst() | 把ResultSet定位到第一行之前。     |
| afterLast()   | 把ResultSet定位到最后一行之后。   |
| first()       | 把ResultSet定位到第一行。         |
| last()        | 把ResultSet定位到最后一行。       |
| absolute(int) | 把ResultSet移动到参数指定的行数。 |
| relative(int) | 向前或者向后移动参数指定的行。    |

#### 获取结果集光标位置

|  方法| 描述|
| --------------- | -------------------- |
| isFirst()       | 是否在一行。         |
| isLast()        | 是否在最后一行。     |
| isBeforeFirst() | 是否在第一行之前。   |
| isAfterLast()   | 是否在最后一行之后。 |
| getRow()        | 获取当前在第几行。   |

#### 获取结果集中的数据

|  方法| 描述|
| ------------------------------------------------------ | ----------------------------- |
| boolean getBoolean(int columnIndex)                    | 按列标获取bool型数据。        |
| boolean getBoolean(String columnLabel)                 | 按列名获取bool型数据。        |
| byte getByte(int columnIndex)                          | 按列标获取Byte型数据。        |
| byte getByte(String columnLabel)                       | 按列名获取Byte型数据。        |
| short getShort(int columnIndex)                        | 按列标获取short型数据。       |
| short getShort(String columnLabel)                     | 按列名获取short型数据。       |
| long getLong(int columnIndex)                          | 按列标获取long型数据。        |
| long getLong(String columnLabel)                       | 按列名获取long型数据          |
| float getFloat(int columnIndex)                        | 按列标获取float型数据。       |
| float getFloat(String columnLabel)                     | 按列名获取float型数据。       |
| double getDouble(int columnIndex)                      | 按列标获取double型数据。      |
| double getDouble(String columnLabel)                   | 按列名获取double型数据        |
| BigDecimal getBigDecimal(int columnIndex)              | 按列标获取BigDecimal 型数据。 |
| BigDecimal getBigDecimal(String columnLabel)           | 按列名获取BigDecimal 型数据。 |
| byte\[\] getBytes(int columnIndex)                     | 按列标获取byte\[\]数据。      |
| byte\[\] getBytes(String columnLabel)                  | 按列名获取byte\[\]数据。      |
| int getInt(int columnIndex)                            | 按列标获取int型数据。         |
| int getInt(String columnLabel)                         | 按列名获取int型数据。         |
| RowId getRowId(int columnIndex)                        | 按列标获取RowId型数据。       |
| RowId getRowId(String columnLabel)                     | 按列名获取RowId型数据。       |
| String getString(int columnIndex)                      | 按列标获取String型数据。      |
| String getString(String columnLabel)                   | 按列名获取String型数据。      |
| Time getTime(int columnIndex) throws SQLException;     | 按列标获取Time型数据。        |
| Time  getTime(String columnLabel) throws SQLException; | 按列名获取Time型数据。        |
| Timestamp getTimestamp(int columnIndex)                | 按列标获取TimeStamp型数据。   |
| Timestamp getTimestamp(String columnLabel)             | 按列名获取TimeStamp型数据。   |
| Date getDate(int columnIndex)                          | 按列标获取Date型数据          |
| Date getDate(String columnLabel)                       | 按列名获取Date型数据。        |

#### **获取流式结果集**

流式结果集开启条件为stmt设置fetchSize为Integer.MIN_VALUE，resultset type为ResultSet.TYPE_FORWARD_ONLY，resultsetConcurrency为ResultSet.CONCUR_READ_ONLY。

示例：
```java
Connection conn= DriverManager.getConnection(url, user, passwd);

Statement stmt = conn.createStatement(ResultSet.TYPE_FORWARD_ONLY, ResultSet.CONCUR_READ_ONLY);
stmt.setFetchSize(Integer.MIN_VALUE);
```

也可通过URL配置connection，此时改connection下创建的stmt默认执行流式fetch，示例：
```java
String url = "jdbc:yasdb://192.168.1.2:1688/yashan?defaultRowFetchSize=-2147483648&netTimeoutForStreamingResults=120";

Connection conn= DriverManager.getConnection(url, user, passwd);

Statement stmt = conn.createStatement();
```

|  参数| 描述|
| ----------------------------- | ------------------------------------------ |
| defaultRowFetchSize           | stmt执行查询时的单次获取行数               |
| netTimeoutForStreamingResults | 开始流式结果集后，服务端发送时阻塞超时时间 |

## JDBC驱动日志配置与打印

YashanDB JDBC驱动在运行时会记录日志，日志分为error、warn、info、debug和trace五种级别。

YashanDB JDBC驱动的日志功能支持SLF4J和JCL（Apache Commons Logging）两种门面技术，支持目前所有的日志框架，例如log4j、log4j2、logback、JUL（Java原生的java.util.logging）等。

为了快速定界业务中出现的性能问题，YashanDB JDBC驱动在获取连接、关闭连接、SQL预编译、绑定执行、直接执行、fetch结果集等操作的开始和结束时都分别加入了日志信息，这些日志的级别为trace。

正常情况下用户无需额外进行日志配置，系统会根据应用所配置的日志级别将JDBC驱动的日志和应用的业务日志一起打印。

### 日志内容

**获取连接**

开始：`connect start,timestamp:{},ip/port:{},serverType:{}`。

结束：`connect end,timestamp:{},session id:{},connectVersion:{}`。

**关闭连接**

开始：`connect close start,timestamp:{},session id:{}`。

开始：`connect close end,timestamp:{},session id:{}`。

**预编译**

开始：`prepare start,timestamp:{},session id:{}, sql:{}`。

结束：`prepare end,timestamp:{},session id:{},statement id:{}`。

**绑定执行**

开始：`prepareStatement execute start,timestamp:{},session id:{}, Statement id:{}`。

结束：`prepareStatement execute end,timestamp:{},session id:{}, Statement id:{}`。

**直接执行**

开始：`statement execute start,timestamp:{}, Statement id:{},sql:{}`。

结束：`statement execute end,timestamp:{},session id:{}, Statement id:{}`。

**fetch**

开始：`fetchMore start,timestamp:{},session id:{}, Statement id:{}`。

结束：`fetchMore end,timestamp:{},session id:{}, Statement id:{}`。

### 日志级别与路径单独配置

交互信息的日志级别为trace，如需打印此类日志只需将应用的日志级别调整成trace级别即可，但此配置会让应用的所有trace级别日志全部被打印，容易引起日志过多等问题。

为避免打印过多无关日志信息，建议单独配置驱动包（com.yashandb）的日志级别和路径，将com.yashandb包的日志级别设置为trace，或将驱动日志和业务日志分开打印在不同路径下。

log4j、log4j2、logback、JUL等主流框架均支持分包设置日志级别和日志路径，以下示例为spring-boot默认的logback日志框架的logging-config.xml写法，其他日志框架配置类似。

> **Note**:
>
> 在JDK原生的JUL日志框架中，日志级别从高到低分别为SEVERE，WARNING，INFO，CONFIG，FINE，FINER，FINEST等，其它日志框架的trace级别对应于JUL的FINEST级别。

```xml
<configuration>
    <contextName>jdbc-mybatis</contextName>
    <property name = "dir" value = "log"/>

    <appender name="consoleLog" class="ch.qos.logback.core.ConsoleAppender">
        <!--展示格式 layout-->
        <layout class="ch.qos.logback.classic.PatternLayout">
            <pattern>
                <pattern>%d{HH:mm:ss.SSS} %contextName [%thread] %-5level %logger{36} - %msg%n</pattern>
            </pattern>
        </layout>

    </appender>

    <appender name="fileLog" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <File>${dir}/project_log.log</File>
        <!--滚动策略，按照时间滚动 TimeBasedRollingPolicy-->
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!--文件路径,定义了日志的切分方式——把每一天的日志归档到一个文件中,以防止日志填满整个磁盘空间-->
            <FileNamePattern>${dir}/project_log.%d{yyyy-MM-dd}.log</FileNamePattern>
            <!--只保留最近90天的日志-->
            <maxHistory>90</maxHistory>

        </rollingPolicy>
        <!--日志输出编码格式化-->
        <encoder>
            <charset>UTF-8</charset>
            <pattern>%d [%thread] %-5level %logger{36} %line - %msg%n</pattern>
        </encoder>
    </appender>

    <appender name="fileLog1" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <File>${dir}/driver_log.log</File>
        <!--滚动策略，按照时间滚动 TimeBasedRollingPolicy-->
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!--文件路径,定义了日志的切分方式——把每一天的日志归档到一个文件中,以防止日志填满整个磁盘空间-->
            <FileNamePattern>${dir}/driver_log.%d{yyyy-MM-dd}.log</FileNamePattern>
            <!--只保留最近90天的日志-->
            <maxHistory>90</maxHistory>

        </rollingPolicy>
        <!--日志输出编码格式化-->
        <encoder>
            <charset>UTF-8</charset>
            <pattern>%d [%thread] %-5level %logger{36} %line - %msg%n</pattern>
        </encoder>
    </appender>
    
    <!-- 单独设置驱动的日志级别为TRACE -->
    <logger name="com.yashandb" level="TRACE" additivity="false">
        <!-- 配置驱动的日志appender为上面定义的fileLog1，即${dir}/driver_log.log -->
        <appender-ref ref="fileLog1"/>
    </logger>

    <!-- 设置dao层的日志级别为DEBUG -->
    <logger name="com.example.dao" level="DEBUG" additivity="false">
        <appender-ref ref="fileLog"/>
    </logger>
    
    <root level="INFO">
        <!--appender将会添加到这个loger-->
        <appender-ref ref="consoleLog1"/>
        <appender-ref ref="fileLog"/>
    </root>

</configuration>
```

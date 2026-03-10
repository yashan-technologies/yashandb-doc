YashanDB JDBC驱动对json数据的插入和取出都支持两种方式：字符串和YasonValue。

### 字符串形式的数据插入和取出

YashanDB支持字符串与json类型的互转，所以对于json类型的数据，可以在java端转化成字符串，然后通过setString方法进行参数绑定来存入数据库，取出时也支持getString接口把服务端存的json数据以字符串格式取出来。

绑定接口为：java.sql.PreparedStatement#setString(int index, String value)。<br/>
取出接口为：java.sql.ResultSet#getString(int index)。

简单示例如下：
```java
    public void testYasonInsert() throws Exception {
        // create table json(id int,c1 json,c2 json);
        String insertSql = "insert into json(id,c1,c2) values(?,?,?)";
        try(Connection connection = getConnection()){
            PreparedStatement ps = connection.prepareStatement(insertSql);
            ps.setInt(1,1);
            String jsonStr1 = "{\"key1\":300.000,\"key2\":3,\"key3\":23.323}";
            String jsonStr2 = "[23,\"value1\",null,3]";
            ps.setString(2,jsonStr1);
            ps.setString(3,jsonStr2);
            ps.executeUpdate();
            Statement statement = connection.createStatement();
            ResultSet resultSet = statement.executeQuery("select * from json where id = 1");
            resultSet.next();
            System.out.println(resultSet.getString(2)); // {"key1":300,"key2":3,"key3":23.323}
            System.out.println(resultSet.getString(3)); // [23,"value1",null,3]
            statement.close();
        }
    }
    
    public static Connection getConnection() throws SQLException, ClassNotFoundException {
        Class.forName("com.yashandb.jdbc.Driver");
        Properties props = new Properties();
        String url="jdbc:yasdb://192.168.1.2:1688/yasdb";
        String user="sales";
        String password="sales";
        props.setProperty("user", user);
        props.setProperty("password", password);
        Connection connection = DriverManager.getConnection(url, props);
        return connection;
    }
```

### 通过YasonValue类型存取json
由于字符串形式操作json数据存在诸多不便，例如不能方便地修改json数据等，所以YashanDB JDBC驱动实现了一整套的json对象模型以及相关工具类，可以很方便的操作json数据，在操作完成后，可以通过setObject方法把json对象存入数据库，取出时也可以通过getObject从结果集中取出json数据。

绑定接口为：<br/>
java.sql.PreparedStatement#setObject(int index, Object value)<br/>
java.sql.PreparedStatement#setObject(int index, Object value, int targetSqlType)

以上三个接口，要绑定json类型的数据时，要求value必须为YasonValue类型，targetSqlType必须为YasTypes.JSON。

可以使用java.sql.ResultSet的如下几个接口从结果集中取出数据：<br/>
Object getObject(int columnIndex)<br/>
Object getObject(String columnLabel)<br/>
\<T> T getObject(int columnIndex, Class\<T> type)<br/>
\<T> T getObject(String columnLabel, Class\<T> type)<br/>

要获取json类型的数据时，传入的type类型应该是YasonValue及其子类型，如果数据实际类型与传入类型不相符的话，会抛异常。

```java
    public void testYasonValueInsert() throws Exception {
        // create table json2(id int,c1 json,c2 json,c3 json);
        String insertSql = "insert into json2(id,c1,c2,c3) values(?,?,?,?)";
        try(Connection connection = getConnection()){
            PreparedStatement ps = connection.prepareStatement(insertSql);
            ps.setInt(1,1);
            YasonObject json1 = new YasonObject();
            json1.put("key1","value1");
            json1.put("key2",new YasonFloat(3.4f));
            json1.put("key3",new byte[]{97,97,97});

            YasonArray json2 = new YasonArray();
            YasonString json3 = new YasonString("yasonString");
            json2.add(3);
            json2.add(LocalDateTime.of(2023,8,23,14,56,1,655900000));
            json2.add(json1);
            json2.add(json3);

            ps.setObject(2,json1);
            ps.setObject(3,json2, YasTypes.JSON);
            ps.setObject(4,json3, YasTypes.JSON);
            ps.executeUpdate();
            Statement statement = connection.createStatement();
            ResultSet resultSet = statement.executeQuery("select * from json2 where id = 1");
            resultSet.next();
            YasonObject json1Result = resultSet.getObject(2,YasonObject.class);
            System.out.println(json1Result.get("key1")); // "value1"
            YasonBinary binary = (YasonBinary) json1Result.get("key3");// [97, 97, 97]
            System.out.println(Arrays.toString(binary.getBytes()));

            YasonArray json2Result = (YasonArray)resultSet.getObject(3);
            System.out.println(json2Result.get(1)); // "2023-08-23T14:56:01.6559"
            System.out.println(json2Result.size()); // 4

            YasonValue json3Result = (YasonValue) resultSet.getObject(4);
            System.out.println(json3Result); // "yasonString"
            System.out.println(json3Result.getType()); // YASON_STRING
            System.out.println(json3Result.equals(json2Result.get(3))); // true
            statement.close();
        }

    }
    
    public static Connection getConnection() throws SQLException, ClassNotFoundException {
        Class.forName("com.yashandb.jdbc.Driver");
        Properties props = new Properties();
        String url="jdbc:yasdb://192.168.1.2:1688/yasdb";
        String user="sales";
        String password="sales";
        props.setProperty("user", user);
        props.setProperty("password", password);
        Connection connection = DriverManager.getConnection(url, props);
        return connection;
    }
```

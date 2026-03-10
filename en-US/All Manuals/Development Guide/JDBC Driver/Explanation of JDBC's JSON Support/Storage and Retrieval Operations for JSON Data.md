YashanDB JDBC driver supports two methods for inserting and retrieving json data: String and YasonValue.

### String Format for Inserting and Retrieving Data

YashanDB supports the conversion between String and json types. Therefore, for json type data, it can be converted to a String on the Java side and then stored in the database using the setString method for parameter binding. When retrieving, the getString interface also supports extracting the json data stored on the server in String format.

The binding interface is: java.sql.PreparedStatement#setString(int index, String value).<br/>
The retrieval interface is: java.sql.ResultSet#getString(int index).

A simple example is as follows:
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

### Accessing json Using YasonValue Type
Due to the inconveniences of manipulating json data in string form—such as difficulties in modifying json data—YashanDB JDBC driver implements a complete json object model and related utility classes, making it easy to operate on json data. After the operations are completed, the json objects can be stored in the database using the setObject method, and can also be retrieved from the result set using getObject.

The binding interfaces are:<br/>
java.sql.PreparedStatement#setObject(int index, Object value)<br/>
java.sql.PreparedStatement#setObject(int index, Object value, int targetSqlType)

For the above three interfaces, when binding json type data, the value must be of YasonValue type, and the targetSqlType must be YasTypes.JSON.

You can use the following interfaces of java.sql.ResultSet to extract data from the result set:<br/>
Object getObject(int columnIndex)<br/>
Object getObject(String columnLabel)<br/>
\<T> T getObject(int columnIndex, Class\<T> type)<br/>
\<T> T getObject(String columnLabel, Class\<T> type)<br/>

To retrieve json type data, the type passed should be YasonValue and its subtypes. An exception will be thrown if the actual data type does not match the passed type.

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

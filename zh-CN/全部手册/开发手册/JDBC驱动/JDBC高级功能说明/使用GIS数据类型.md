YashanDB中的ST_GEOMETRY类型是数据库内置的一种自定义类型，用于存储和访问符合开放地理空间信息联盟（Open Geospatial Consortium，简称OGC）制定的SFA SQL标准的几何对象。

ST_GEOMETRY实际上是一种内置的UDT Object类型，具备如下属性：

|  **属性名称**| 类型| 长度|
|--------|------|-------|
| HEAD   | RAW | 40字节  |
| GEOM   | BLOB| < 4G  |

### 将WKT数据插入ST\_GEOMETRY列

如需将WKT数据插入ST_GEOMETRY列中，可以使用ST_GEOMFROMTEXT函数把WKT格式的字符串转成ST_GEOMETRY类型的数据。

示例如下：
```java
        try(Connection connection = Util.getYasConnection()) {
            // 表结构为：create table table1(id int,position  ST_GEOMETRY);
            PreparedStatement preparedStatement = connection.prepareStatement("insert into table1 values (?,ST_GEOMFROMTEXT(?))");
            //各种类型的WKT数据
            String[] values = {"POINT(0 0)",
                    "LINESTRING (1 2,4 5)",
                    "POLYGON ((1 0,1 1,2 2,1 0),(0 0,6 6,8 8,0 0))",
                    "MULTIPOINT ((1 1),(2 2))",
                    "MULTILINESTRING ((1 2,4 5),(2 3,5 6))",
                    "MULTIPOLYGON (((1 5, 4 3, 6 6, 2 6, 1 5)), ((6 5, 8 8, 6 9, 6 5)))",
                    "GEOMETRYCOLLECTION (POINT (1 0),LINESTRING (1 2,4 5))"};
            for (int i = 0; i < values.length; i++) {
                preparedStatement.setInt(1,i);
                preparedStatement.setString(2,values[i]);
                preparedStatement.addBatch();
            }
            preparedStatement.executeBatch();

        }
```

### 将WKB数据插入ST\_GEOMETRY列

如需将WKB数据要插入ST_GEOMETRY列中，可以使用ST_GEOMFROMWKB函数把WKB数据转成ST_GEOMETRY类型的数据，WKB数据通常是BLOB类型或byte[]类型。

示例如下：
```java
        try(Connection connection = Util.getYasConnection()) {
            PreparedStatement preparedStatement = connection.prepareStatement("insert into table1 values (?,ST_GEOMFROMWKB(?))");
            byte[] bytes1 = {1, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0};
            byte[] bytes2 = {1, 2, 0, 0, 0, 2, 0, 0, 0, 0, 0, 0, 0, 0, 0, -16, 63, 0, 0, 0, 0, 0, 0, 0, 64, 0, 0, 0, 0, 0, 0, 16, 64, 0, 0, 0, 0, 0, 0, 20, 64};
            Blob blob = connection.createBlob();
            blob.setBytes(1,bytes2);

            preparedStatement.setInt(1,1);
            preparedStatement.setBytes(2,bytes1);
            preparedStatement.addBatch();

            preparedStatement.setInt(1,2);
            preparedStatement.setBlob(2,blob);
            preparedStatement.addBatch();

            preparedStatement.executeBatch();

        }
```

### 查询ST\_GEOMETRY各种形式的数据

如需查询ST_GEOMETRY各种形式的数据，可以使用ST_ASTEXT、ST_ASBINARY、ST_ASEWKB以及ST_ASGEOJSON等函数分别查询WKT形式、WKB形式、EWKB形式和JSON形式的数据，也可以直接查ST_GEOMETRY列获取完整的Struct类型的数据，然后从Struct中分别取得HEAD数据和BLOB类型的数据，再从BLOB中获取WKB形式的数据。

示例如下：
```java
         try(Connection connection = Util.getYasConnection()) {
            Statement stmt = connection.createStatement();
            ResultSet rs = stmt.executeQuery("select id, ST_ASTEXT(position) , ST_ASBINARY(position), ST_ASEWKB"
                    + "(position), ST_ASGEOJSON(position),position from table1");
            while (rs.next()) {
                String wkt = rs.getString(2);
                System.out.println(wkt); // POINT (0.000000000000000 0.000000000000000)
                byte[] wtb = rs.getBytes(3);
                System.out.println(Arrays.toString(wtb)); // [1, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
                byte[] ewtb = rs.getBytes(4);
                System.out.println(Arrays.toString(ewtb)); // [1, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
                String geoJson = rs.getString(5);
                System.out.println(geoJson); // {"type":"Point","coordinates":[0.0,0.0]}
                Struct geometryObject = (Struct) rs.getObject(6);
                Object[] values = geometryObject.getAttributes();
                byte[] head = (byte[]) values[0];
                System.out.println(Arrays.toString(head)); // [0, 1, 20, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
                Blob blob = (Blob) values[1];
                byte[] wtbFromBlob = blob.getBytes(1,(int)blob.length());
                System.out.println(Arrays.toString(wtbFromBlob)); // [1, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
            }
        }
```

### 插入UDT Object类型的ST\_GEOMETRY数据

除了上面提到的使用数据库函数的方式插入WKT或者WKB形式的ST_GEOMETRY数据外，基于其本质上是一个UDT Object类型，还可以使用Struct类型进行参数绑定。只需获取到HEAD和WKB数据即可组装出Struct对象，然后通过参数绑定进行数据插入。但在插入前通常无法获取HEAD信息，因此如需使用参数绑定的方式插入ST_GEOMETRY数据，只能插入从数据库里查出的数据。

示例如下：
```java
        try(Connection connection = Util.getYasConnection()) {
            Statement stmt = connection.createStatement();
            ResultSet rs = stmt.executeQuery("select id,position from table1");
            rs.next();
            Struct geometryObject = (Struct) rs.getObject(2);
            
            PreparedStatement preparedStatement = connection.prepareStatement("insert into table1 values (?,?)");
            
            preparedStatement.setInt(1,3);
            preparedStatement.setObject(2,geometryObject); // 直接把查出来的Struct绑定进去进行插入
            preparedStatement.addBatch();

            // 根据查询所得Struct的Attributes创建新的Struct，再进行参数绑定插入数据
            Struct geometryObject2 = connection.createStruct("MDSYS.ST_GEOMETRY",geometryObject.getAttributes());
            preparedStatement.setInt(1,4);
            preparedStatement.setObject(2,geometryObject2); 
            preparedStatement.addBatch();
            preparedStatement.executeBatch();

            // 根据查询所得Struct的Attributes取出HEAD和BLOB类型的GEOM数据，再从GEOM中取出WKB数据重新构造。使用HEAD与新构造的BLOB一起组装出新的Aattributes来创建Struct，再进行参数绑定插入数据
            Blob geom = (Blob) geometryObject.getAttributes()[1];
            byte[] wtbFromBlob = geom.getBytes(1,(int)geom.length());
            Blob blob1 = connection.createBlob();
            blob1.setBytes(1,wtbFromBlob);
            Object[] attributes = new Object[2];
            attributes[0] = geometryObject.getAttributes()[0]; // head信息
            attributes[1] = blob1;
            Struct geometryObject3 = connection.createStruct("MDSYS.ST_GEOMETRY",attributes);
            preparedStatement.setInt(1,5);
            preparedStatement.setObject(2,geometryObject3);
            preparedStatement.addBatch();
            
            preparedStatement.executeBatch();

        }
```

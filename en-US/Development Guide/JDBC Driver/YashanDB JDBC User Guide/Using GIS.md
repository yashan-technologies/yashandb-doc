The ST_GEOMETRY type in YashanDB is a built-in UDT used to store and access geometric objects that comply with the Spatial SQL standard defined by the Open Geospatial Consortium (OGC).

ST_GEOMETRY is essentially a built-in UDT Object type with the following attributes:

|**Attribute Name** |Type |Length |
|--------|------|-------|
| HEAD              | RAW    | 40 bytes  |
| GEOM   | BLOB| < 4G  |

### Inserting WKT Data into ST_GEOMETRY Column

To insert WKT data into an ST_GEOMETRY column, you can use the ST_GEOMFROMTEXT function to convert a WKT formatted string into ST_GEOMETRY type data.

***Example***:
```java
        try(Connection connection = Util.getYasConnection()) {
            // Table structure: create table table1(id int, position ST_GEOMETRY);
            PreparedStatement preparedStatement = connection.prepareStatement("insert into table1 values (?,ST_GEOMFROMTEXT(?))");
            // Various types of WKT data
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

### Inserting WKB Data into ST_GEOMETRY Column

To insert WKB data into an ST_GEOMETRY column, you can use the ST_GEOMFROMWKB function to convert the WKB data into ST_GEOMETRY type data. WKB data is usually of BLOB type or byte[] type.

***Example***:
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

### ```

### Querying Various Forms of ST_GEOMETRY Data

To query various forms of ST_GEOMETRY data, you can use functions such as ST_ASTEXT, ST_ASBINARY, ST_ASEWKB, and ST_ASGEOJSON to retrieve data in WKT format, WKB format, EWKB format, and JSON format respectively. You can also directly query the ST_GEOMETRY column to get the complete Struct type data, then obtain HEAD data and the BLOB type data from the Struct, and finally extract WKB format data from the BLOB.
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

### Inserting UDT Object Type ST_GEOMETRY Data

In addition to the methods mentioned above for inserting WKT or WKB format ST_GEOMETRY data using database functions, you can also use the Struct type for parameter binding. Simply obtain the HEAD and WKB data to construct the Struct object, and then insert it using parameter binding. However, typically you cannot obtain HEAD information before insertion, so when using parameter binding to insert ST_GEOMETRY data, you can only insert data retrieved from the database.

***Example***:
```java
        try(Connection connection = Util.getYasConnection()) {
            Statement stmt = connection.createStatement();
            ResultSet rs = stmt.executeQuery("select id,position from table1");
            rs.next();
            Struct geometryObject = (Struct) rs.getObject(2);
            
            PreparedStatement preparedStatement = connection.prepareStatement("insert into table1 values (?,?)");
            
            preparedStatement.setInt(1,3);
            preparedStatement.setObject(2, geometryObject); // Directly bind the retrieved Struct for insertion
            preparedStatement.addBatch();

            // Create a new Struct based on the Attributes of the queried Struct, then bind the parameters for data insertion
            Struct geometryObject2 = connection.createStruct("MDSYS.ST_GEOMETRY",geometryObject.getAttributes());
            preparedStatement.setInt(1,4);
            preparedStatement.setObject(2,geometryObject2); 
            preparedStatement.addBatch();
            preparedStatement.executeBatch();

            // Extract HEAD and BLOB type GEOM data from the queried Struct, then reconstruct the WKB data. Use HEAD and the newly constructed BLOB to assemble new Attributes for creating Struct, then bind the parameters for data insertion
            Blob geom = (Blob) geometryObject.getAttributes()[1];
            byte[] wtbFromBlob = geom.getBytes(1,(int)geom.length());
            Blob blob1 = connection.createBlob();
            blob1.setBytes(1,wtbFromBlob);
            Object[] attributes = new Object[2];
            attributes[0] = geometryObject.getAttributes()[0]; // HEAD information
            attributes[1] = blob1;
            Struct geometryObject3 = connection.createStruct("MDSYS.ST_GEOMETRY",attributes);
            preparedStatement.setInt(1,5);
            preparedStatement.setObject(2,geometryObject3);
            preparedStatement.addBatch();
            
            preparedStatement.executeBatch();

        }
```

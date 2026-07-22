A vector is a set of unstructured data used to represent the features of an object, where the object can be a document, image, video, audio file, etc.

Yashan Database, based on its original relational data processing model, treats vectors as a native data type, supporting vector data storage, querying, and similarity calculation.

The JDBC driver also supports parameter binding and query retrieval for vector types, as well as necessary type conversions.

## Interface Methods

Use the two static methods `ofFloat64Values(Object values)` and `ofFloat32Values(Object values)` from `com.yashandb.vector.Vector` to create a Vector object. The former is Float64 type and the latter is Float32 type.

Use the following interface methods to read and write Vector objects:

| Class                      | Method                                                                                                      | Description                                                                                                    |
|----------------------------|-------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------|
| java.sql.ResultSet         | getObject(int index)<br> getObject(String columnName)                                                       |                                                                                                                |
| java.sql.PreparedStatement | setObject(int index, Vector vector)<br> setObject(int index, Vector vector, int targetSqlType, int scale)   | We define the type code `YasTypes.VECTOR` = 3012. When targetSqlType parameter is `YasTypes.VECTOR`, VECTOR type is bound by default |

All interfaces of com.yashandb.vector.Vector class are as follows:

| Method                                           | Description                                                                                              |
|--------------------------------------------------|----------------------------------------------------------------------------------------------------------|
| static Vector fromData(byte[] data)              | Get Vector object from encoded data                                                                      |
| static Vector ofFloat64Values(Object values)     | values can be numeric arrays of various types, such as int[], double[], float[], long[], short[]<br/>as well as boxed type arrays of the above. Convert numeric array vector values to Float64 type Vector object |
| static Vector ofFloat32Values(Object values)     | Same specifications as above. Convert numeric array vector values to Float32 type Vector object          |
| float[] toFloatArray()                           | Get vector values from Vector object as float[] type                                                     |
| double[] toDoubleArray()                         | Get vector values from Vector object as double[] type                                                    |
| static float[] toFloatArray(Object values)       | Same specifications as above. Convert numeric array vector values to float[] type vector values          |
| static double[] toDoubleArray(Object values)     | Same specifications as above. Convert numeric array vector values to double[] type vector values         |

## Vector Object Creation Practice

Use `ofFloat64Values(Object values)` and `ofFloat32Values(Object values)` from `com.yashandb.vector` to create Vector objects.

### Use PreparedStatement to Bind Vector Variables for Insertion

```java
package quickstart;
import java.sql.*;
import java.util.*;

public class VectorWithPreparedStatementExample {
    public static void main(String[] args) {
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");
        
        // Load driver first
        try {
            Class.forName("com.yashandb.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            return;
        }
        
        Connection conn = null;
        try {
            conn = DriverManager.getConnection(url, info);
            conn.setAutoCommit(false); 
            
            // Create table first if not exists
            String createTableSql = "CREATE TABLE VECTOR_DATA1 (" +
                    "ID INT PRIMARY KEY," +
                    "C1 VECTOR(4, FLOAT64))";
            try (Statement stmt = conn.createStatement()) {
                stmt.execute(createTableSql);
                System.out.println("Table created or already exists");
            } catch (SQLException e) {
                System.out.println("Create table info: " + e.getMessage());
            }
            
            // Batch insert data
            String sql ="insert into VECTOR_DATA1 values (?,?)";
            try (PreparedStatement ps = conn.prepareStatement(sql)) {
                // Prepare multiple VECTOR records
               List values = new ArrayList();
               values.add(new double[]{0.1,0.2,0.3,0.4});
               values.add(new float[]{0.1f,0.2f,0.3f,0.4f});
               values.add(new short[]{1,2,3, 4});
               values.add(new int[]{1,2,3, 4});
               values.add(new long[]{1,2,3, 4});
               values.add(new Float[]{0.1f,0.2f,0.3f,0.4f});
               values.add(new Double[]{0.1,0.2,0.3,0.4});
               
               for (int i = 0; i < values.size(); i++) {
                  ps.setInt(1,i);
                  ps.setObject(2,Vector.ofFloat64Values(values.get(i)));
                  ps.addBatch();
                  
               }
               
               // Execute batch processing
               int[] updateCounts = ps.executeBatch();
               int totalAffected = java.util.Arrays.stream(updateCounts).sum();
               System.out.println("Batch insert completed, total affected: " + totalAffected);
               // Commit transaction
               conn.commit();
            } catch (SQLException e) {
                System.out.println("Error in batch processing: " + e.getMessage());
                e.printStackTrace();
            }
        } catch (SQLException e) {
            System.out.println("Error: " + e.getMessage());
            e.printStackTrace();
        } finally {
            if (conn != null) {
                try {
                    conn.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```

###  Reading Vector values from a ResultSet

```java
package quickstart;
import java.sql.*;
import java.util.*;

public class VectorFromResultsetExample {
    public static void main(String[] args) {
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        // Load driver first
        try {
            Class.forName("com.yashandb.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            return;
        }
        
        try (Connection conn = DriverManager.getConnection(url, info); Statement statement = conn.createStatement()) {
            
            conn.setAutoCommit(false); 

            //  Create table first if not exists
            String createTableSql = "CREATE TABLE VECTOR_DATA2 (" +
                    "ID INT PRIMARY KEY," +
                    "C1 VECTOR(4, FLOAT64))";

            statement.execute(createTableSql);
            //insert value
            statement.execute("insert into VECTOR_DATA2 values (1,TO_VECTOR('[0.1, 0.2, 0.3, 0.4]', 4, FLOAT64))");  

            // query
            String sql ="select * from VECTOR_DATA2";
            ResultSet resultSet = statement.executeQuery(sql);
            while (resultSet.next()) {
            
              Vector vector = (Vector) resultSet.getObject(2);
              double[] doublesValue = vector.toDoubleArray();
              float[] floatsValue = vector.toFloatArray();
              System.out.println(Arrays.toString(doublesValue)); // [0.1, 0.2, 0.3, 0.4]
            
            }
        } catch (SQLException e) {
            System.out.println("Error: " + e.getMessage());
            e.printStackTrace();
        }
    }
}
```
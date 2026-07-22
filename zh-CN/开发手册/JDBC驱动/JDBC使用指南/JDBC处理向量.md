向量是一组用于表示一个对象的特征的非结构化数据，面向的对象可以是文档、图像、视频或音频文件等。

YashanDB在原有关系数据处理模型的基础上，将向量作为一种原生数据类型，支持向量数据存储、查询和相似性计算。

JDBC驱动也支持向量类型的参数绑定和查询获取，以及必要的类型转换。

## 接口方法

使用com.yashandb.vector.Vector的ofFloat64Values(Object values)和ofFloat32Values(Object values)两个静态方法可以得到一个Vector对象，前者为Float64类型，后者为Float32类型。

使用如下接口方法读写Vector对象：

| 类                          | 方法                                                                                                        | 说明                                                                            |
|----------------------------|-----------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
| java.sql.ResultSet         | getObject(int index)<br> getObject(String columnName)                                                     |                                                                               |
| java.sql.PreparedStatement | setObject(int index, Vector vector)<br> setObject(int index, Vector vector, int targetSqlType, int scale) | 我们定义了类型编码`YasTypes.VECTOR` = 3012，targetSqlType参数值为`YasTypes.VECTOR`时默认绑定VECTOR类型 |

com.yashandb.vector.Vector类的所有接口如下：

| 方法                                           | 说明                                                                                                          |
|----------------------------------------------|-------------------------------------------------------------------------------------------------------------|
| static Vector fromData(byte[] data)          | 根据编码好的数据获取Vector对象                                                                                          |
| static Vector ofFloat64Values(Object values) | values可以为各种类型的数字数组，比如int[]，double[]，float[]，long[]，short[]<br/>以及他们的装箱类型的数组，把数字数组形式的向量值转为Float64类型的Vector对象 |
| static Vector ofFloat32Values(Object values) | values规格同上，把数字数组形式的向量值转为Float32类型的Vector对象                                                                  |
| float[] toFloatArray()                       | 从Vector对象中获取float[]类型的向量值                                                                                   |
| double[] toDoubleArray()                     | 从Vector对象中获取double[]类型的向量值                                                                                  |
| static float[] toFloatArray(Object values)   | values规格同上，把数字数组形式的向量值转为float[]形式的向量值                                                                       |
| static double[] toDoubleArray(Object values) | values规格同上，把数字数组形式的向量值转为double[]形式的向量值                                                                      |

## 创建Vector对象实践

使用com.yashandb.vector的ofFloat64Values(Object values)和ofFloat32Values(Object values)创建Vector对象。

### 使用PreparedStatement绑定Vector变量实现插入

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

        // 先加载驱动
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

            // 先创建表（如果不存在）
            String createTableSql = "CREATE TABLE VECTOR_DATA1 (" +
                    "ID INT PRIMARY KEY," +
                    "C1 VECTOR(4, FLOAT64))";
            try (Statement stmt = conn.createStatement()) {
                stmt.execute(createTableSql);
                System.out.println("Table created or already exists");
            } catch (SQLException e) {
                System.out.println("Create table info: " + e.getMessage());
            }

            // 批量插入XML数据
            String sql ="insert into VECTOR_DATA1 values (?,?)";
            try (PreparedStatement pstmt = conn.prepareStatement(sql)) {
                // 准备多条VECTOR记录
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

                // 执行批处理
                int[] updateCounts = ps.executeBatch();
                int totalAffected = java.util.Arrays.stream(updateCounts).sum();
                System.out.println("Batch insert completed, total affected: " + totalAffected);
                // 提交事务
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

### 从结果集中读取Vector的值

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

        // 先加载驱动
        try {
            Class.forName("com.yashandb.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            return;
        }
        
        try (Connection conn = DriverManager.getConnection(url, info); Statement statement = conn.createStatement()) {
            
            conn.setAutoCommit(false); 

            // 先创建表（如果不存在）
            String createTableSql = "CREATE TABLE VECTOR_DATA2 (" +
                    "ID INT PRIMARY KEY," +
                    "C1 VECTOR(4, FLOAT64))";

            statement.execute(createTableSql);
            //执行插入
            statement.execute("insert into VECTOR_DATA2 values (1,TO_VECTOR('[0.1, 0.2, 0.3, 0.4]', 4, FLOAT64))");  

            // 查询
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



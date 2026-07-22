## 描述

Blob接口用于操作BLOB类型数据。

`java.sql.Blob`接口的核心目标是安全、高效地操作数据库中的大二进制数据，方法主要分为数据读取、数据写入、数据更新、资源管理四类。


详细接口说明请参考[JDBC官方网站](https://docs.oracle.com/en/java/javase/17/docs/api/java.sql/java/sql/Blob.html)。

## 方法

YashanDB JDBC驱动对BLOB接口有如下方法：

| 方法|  返回类型| 备注|
|------------------|----------------------|---------------|
| length()         | long                 |           -  |
| getBytes(long pos, int length)         | byte[]               |   -          |
| setBytes(long pos, byte[] bytes)       | int                  |     -        |
| setBytes(long pos, byte[] bytes, int offset, int len) | int                  | 存算一体分布式集群部署中只能调用本接口将数据写入至CN节点的临时LOB中，无法将数据写至DN节点上。 |
| setBinaryStream(long pos)      | java.io.OutputStream |   -          |
| getBinaryStream() | java.io.InputStream  |   -          |
| getBinaryStream(long pos, long length)    | java.io.InputStream  | -            |
| free()             | void                 |  -           |
| void truncate(long len)          | void                 | 不适用于存算一体分布式集群部署。       |

## 示例

```java
package quickstart;

import java.io.InputStream;
import java.io.OutputStream;
import java.sql.Blob;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.util.Properties;

public class BlobExample {

    public static Connection getConnection() throws SQLException {
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");
        Class.forName("com.yashandb.jdbc.Driver");
        return DriverManager.getConnection(url, info);
    }

    // 1. length() 方法示例
    public void demoLength(Connection conn) throws SQLException {
        Blob blob = conn.createBlob();
        blob.setBytes(1, "Hello".getBytes());
        long length = blob.length();
        System.out.println("BLOB length: " + length);
    }

    // 2. getBytes() 方法示例
    public void demoGetBytes(Connection conn) throws SQLException {
        Blob blob = conn.createBlob();
        blob.setBytes(1, "Hello World".getBytes());
        byte[] part = blob.getBytes(7, 5); // Gets "World"
        System.out.println("Retrieved partial data: " + new String(part));
    }

    // 3. setBytes() 方法示例
    public void demoSetBytes(Connection conn) throws SQLException {
        Blob blob = conn.createBlob();
        blob.setBytes(1, "Hello".getBytes());
        blob.setBytes(6, " World".getBytes()); // Append
        System.out.println("Complete data: " + new String(blob.getBytes(1, 11)));
    }

    // 4. setBytes() 带偏移量示例
    public void demoSetBytesWithOffset(Connection conn) throws SQLException {
        Blob blob = conn.createBlob();
        byte[] data = "ABCDEFGHIJ".getBytes();
        blob.setBytes(1, data, 2, 5); // Start from offset 2, take 5 bytes
        System.out.println("Result: " + new String(blob.getBytes(1, 5))); // Outputs "CDEFG"
    }

    // 5. setBinaryStream() 方法示例
    public void demoSetBinaryStream(Connection conn) throws Exception {
        Blob blob = conn.createBlob();
        try (OutputStream os = blob.setBinaryStream(1)) {
            os.write("Stream Data".getBytes());
        }
        System.out.println("Stream writing completed, length: " + blob.length());
    }
    // 6. getBinaryStream() 方法示例
    public void demoGetBinaryStream(Connection conn) throws Exception {
        Blob blob = conn.createBlob();
        blob.setBytes(1, "Stream Example".getBytes());
        try (InputStream is = blob.getBinaryStream()) {
            byte[] buffer = new byte[1024];
            int bytesRead = is.read(buffer);
            System.out.println("Stream reading: " + new String(buffer, 0, bytesRead));
        }
    }

    // 7. getBinaryStream() 带位置和长度示例 (YashanDB扩展方法)
    // 注意: 这是YashanDB JDBC驱动的扩展方法，非JDBC标准
    public void demoGetBinaryStreamPartial(Connection conn) throws Exception {
        Blob blob = conn.createBlob();
        blob.setBytes(1, "Partial Stream Access".getBytes());
        // 使用YashanDB扩展方法: getBinaryStream(long pos, long length)
        try (InputStream is = blob.getBinaryStream(9, 6)) {
            byte[] data = is.readAllBytes();
            System.out.println("Partial reading: " + new String(data)); // Outputs "Stream"
        }
    }

    // 8. free() 方法示例
    public void demoFree(Connection conn) throws SQLException {
        Blob blob = conn.createBlob();
        blob.setBytes(1, "Test".getBytes());
        System.out.println("Length before freeing: " + blob.length());
        blob.free(); // Release resources
    }

    // 9. truncate() 方法示例
    public void demoTruncate(Connection conn) throws SQLException {
        Blob blob = conn.createBlob();
        blob.setBytes(1, "Very Long String".getBytes());
        blob.truncate(4); // Truncate to 4 bytes
        System.out.println("After truncation: " + new String(blob.getBytes(1, 4))); // Outputs "Very"
    }

    // 10. position() 方法示例 - 查找字节模式
    public void demoPosition(Connection conn) throws SQLException {
        Blob blob = conn.createBlob();
        blob.setBytes(1, "Hello World Hello".getBytes());
        long pos = blob.position("World".getBytes(), 1);
        System.out.println("Position of 'World': " + pos); // Outputs 7
    }
}
```


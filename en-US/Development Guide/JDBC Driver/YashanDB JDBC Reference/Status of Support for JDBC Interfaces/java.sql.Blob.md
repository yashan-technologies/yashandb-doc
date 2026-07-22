## Description

The Blob interface represents that it is used to manipulate BLOB type data.

The core purpose of the `java.sql.Blob` interface is to safely and efficiently manipulate large binary data in the database. Its methods are mainly divided into four categories: data reading, data writing, data updating, and resource management.


Detailed interface specifications, please refer to [The JDBC Official Website](https://docs.oracle.com/en/java/javase/17/docs/api/java.sql/java/sql/Blob.html).

## Method

The YashanDB JDBC driver has the following methods for the BLOB interface:

|Method |Return Type |Notes |
|------------------|----------------------|---------------|
| length()         | long                 |           -  |
| getBytes(long pos, int length)         | byte[]               |   -          |
| setBytes(long pos, byte[] bytes)       | int                  |     -        |
| setBytes(long pos, byte[] bytes, int offset, int len) | int                  | This interface can only be called in ISC Distributed Cluster Deployment to write data into the temporary LOB on the CN node and cannot write data to the DN node. |
| setBinaryStream(long pos)      | java.io.OutputStream |   -          |
| getBinaryStream() | java.io.InputStream  |   -          |
| getBinaryStream(long pos, long length)    | java.io.InputStream  | -            |
| free()             | void                 |  -           |
| void truncate(long len)                      | void                 | Not applicable in ISC Distributed Cluster Deployment.                                                    |

## Example

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

    // 1. length() method example
    public void demoLength(Connection conn) throws SQLException {
        Blob blob = conn.createBlob();
        blob.setBytes(1, "Hello".getBytes());
        long length = blob.length();
        System.out.println("BLOB length: " + length);
    }

    // 2. getBytes() method example
    public void demoGetBytes(Connection conn) throws SQLException {
        Blob blob = conn.createBlob();
        blob.setBytes(1, "Hello World".getBytes());
        byte[] part = blob.getBytes(7, 5); // Gets "World"
        System.out.println("Retrieved partial data: " + new String(part));
    }

    // 3. setBytes() method example
    public void demoSetBytes(Connection conn) throws SQLException {
        Blob blob = conn.createBlob();
        blob.setBytes(1, "Hello".getBytes());
        blob.setBytes(6, " World".getBytes()); // Append
        System.out.println("Complete data: " + new String(blob.getBytes(1, 11)));
    }

    // 4. setBytes() example with offset
    public void demoSetBytesWithOffset(Connection conn) throws SQLException {
        Blob blob = conn.createBlob();
        byte[] data = "ABCDEFGHIJ".getBytes();
        blob.setBytes(1, data, 2, 5); // Start from offset 2, take 5 bytes
        System.out.println("Result: " + new String(blob.getBytes(1, 5))); // Outputs "CDEFG"
    }

    // 5. setBinaryStream() method example
    public void demoSetBinaryStream(Connection conn) throws Exception {
        Blob blob = conn.createBlob();
        try (OutputStream os = blob.setBinaryStream(1)) {
            os.write("Stream Data".getBytes());
        }
        System.out.println("Stream writing completed, length: " + blob.length());
    }
    // 6. getBinaryStream() method example
    public void demoGetBinaryStream(Connection conn) throws Exception {
        Blob blob = conn.createBlob();
        blob.setBytes(1, "Stream Example".getBytes());
        try (InputStream is = blob.getBinaryStream()) {
            byte[] buffer = new byte[1024];
            int bytesRead = is.read(buffer);
            System.out.println("Stream reading: " + new String(buffer, 0, bytesRead));
        }
    }

    // 7. getBinaryStream() example with position and length (YashanDB extended method)
    // Note: This is an extended method of YashanDB JDBC driver, not a JDBC standard
    public void demoGetBinaryStreamPartial(Connection conn) throws Exception {
        Blob blob = conn.createBlob();
        blob.setBytes(1, "Partial Stream Access".getBytes());
        // Use YashanDB extended method: getBinaryStream(long pos, long length)
        try (InputStream is = blob.getBinaryStream(9, 6)) {
            byte[] data = is.readAllBytes();
            System.out.println("Partial reading: " + new String(data)); // Outputs "Stream"
        }
    }

    // 8. free() method example
    public void demoFree(Connection conn) throws SQLException {
        Blob blob = conn.createBlob();
        blob.setBytes(1, "Test".getBytes());
        System.out.println("Length before freeing: " + blob.length());
        blob.free(); // Release resources
    }

    // 9. truncate() method example
    public void demoTruncate(Connection conn) throws SQLException {
        Blob blob = conn.createBlob();
        blob.setBytes(1, "Very Long String".getBytes());
        blob.truncate(4); // Truncate to 4 bytes
        System.out.println("After truncation: " + new String(blob.getBytes(1, 4))); // Outputs "Very"
    }

    // 10. position() method example - find byte pattern
    public void demoPosition(Connection conn) throws SQLException {
        Blob blob = conn.createBlob();
        blob.setBytes(1, "Hello World Hello".getBytes());
        long pos = blob.position("World".getBytes(), 1);
        System.out.println("Position of 'World': " + pos); // Outputs 7
    }
}
```


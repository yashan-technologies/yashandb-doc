## Description

The SQLOutput interface represents that it is used in the parameter binding process to map user-defined Java type objects to UDT types in the database.

`java.sql.SQLOutput` represents that it is the core interface in the JDBC specification for writing field values to database User-Defined Types (UDTs). When a Java class that implements the `SQLData` interface executes the `writeSQL()` method, the JDBC driver automatically creates an `SQLOutput` instance. Developers can use the writing methods of this instance (such as `writeInt()`, `writeString()`) to write the attribute values of a Java object to the corresponding fields of a database user-defined type (such as `STRUCT`, composite type) in sequence.


Detailed interface specifications, please refer to [The JDBC Official Website](https://docs.oracle.com/en/java/javase/17/docs/api/java.sql/java/sql/SQLOutput.html).

## Method

The methods included in `java.sql.SQLOutput` are as follows:

|Method |Return Type |Notes |
|----------------------------------------------------|------|------------------|
| writeString(String) throws SQLException          | void        | Input type: String   |
| writeNString(String) throws SQLException         | void        | Input type: String   |
| writeBoolean(boolean) throws SQLException        | void        | Input type: boolean  |
| writeByte(byte) throws SQLException              | void        | Input type: byte     |
| writeShort(short) throws SQLException            | void        | Input type: short    |
| writeInt(int) throws SQLException                | void        | Input type: int      |
| writeLong(long) throws SQLException              | void        | Input type: long     |
| writeFloat(float) throws SQLException            | void        | Input type: float    |
| writeDouble(double) throws SQLException          | void        | Input type: double   |
| writeBigDecimal(BigDecimal) throws SQLException  | void        | Input type: BigDecimal|
| writeBytes(byte[]) throws SQLException           | void        | Input type: byte[]   |
| writeDate(Date) throws SQLException              | void        | Input type: Date     |
| writeTime(Time) throws SQLException              | void        | Input type: Time     |
| writeTimestamp(TImeStamp) throws SQLException    | void        | Input type: TImeStamp|
| writeObject(Object) throws SQLException          | void        | Input type: Object   |
| writeObject(Class<T> type) throws SQLException   | void        | Input type: Class    |
| writeArray(Array) throws SQLException            | void        | Input type: Array    |
| writeRowId(RowID) throws SQLException            | void        | Input type: RowID    |
| writeBlob(Blob) throws SQLException              | void        | Input type: Blob     |
| writeClob(Clob) throws SQLException              | void        | Input type: Clob     |
| writeNClob(NClob) throws SQLException            | void        | Input type: NClob    |
| writeSQLXML(SQLXML) throws SQLException          | void        | Input type: SQLXML   |
| writeCharacterStream(Reader) throws SQLException | void        | Input type: Reader    |
| writeAsciiStream(InputStream) throws SQLException| void        | Input type: InputStream |
| writeBinaryStream(InputStream) throws SQLException| void        | Input type: InputStream |

## Example

```java
package quickstart;

import java.io.InputStream;
import java.io.Reader;
import java.math.BigDecimal;
import java.sql.Array;
import java.sql.Blob;
import java.sql.CallableStatement;
import java.sql.Clob;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.NClob;
import java.sql.RowId;
import java.sql.SQLData;
import java.sql.SQLInput;
import java.sql.SQLException;
import java.sql.SQLOutput;
import java.sql.Struct;
import java.sql.Time;
import java.sql.Timestamp;
import java.util.Date;
import java.util.HashMap;
import java.util.Map;
import java.util.Properties;

public class Employee implements SQLData {
    private int id;
    private String name;
    private String nName;
    private boolean active;
    private byte level;
    private short dept;
    private int age;
    private long empNo;
    private float bonusRate;
    private double salary;
    private BigDecimal bonus;
    private byte[] photo;
    private Date hireDate;
    private Time startTime;
    private Timestamp lastLogin;
    private Map<String, Object> metadata;
    private Array skills;
    private Blob resume;
    private Clob bio;

    // Getters and Setters
    public int getId() { return id; }
    public void setId(int id) { this.id = id; }
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }

    public static Connection getConnection() throws SQLException {
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");
        Class.forName("com.yashandb.jdbc.Driver");
        return DriverManager.getConnection(url, info);
    }

    @Override
    public String getSQLTypeName() throws SQLException {
        return "EMPLOYEE_TYPE";
    }

    @Override
    public void writeSQL(SQLOutput stream) throws SQLException {
        // Basic types
        stream.writeInt(id);
        stream.writeString(name);
        stream.writeNString(nName);
        stream.writeBoolean(active);
        stream.writeByte(level);
        stream.writeShort(dept);
        stream.writeInt(age);
        stream.writeLong(empNo);
        stream.writeFloat(bonusRate);
        stream.writeDouble(salary);

        // Large numeric
        stream.writeBigDecimal(bonus != null ? bonus : BigDecimal.ZERO);

        // Binary
        stream.writeBytes(photo != null ? photo : new byte[0]);

        // Date and time
        stream.writeDate(hireDate);
        stream.writeTime(startTime);
        stream.writeTimestamp(lastLogin != null ? lastLogin : new Timestamp(System.currentTimeMillis()));

        // Object
        stream.writeObject(metadata);

        // Array
        stream.writeArray(skills);

        // Large objects
        stream.writeBlob(resume);
        stream.writeClob(bio);
    }

    // Usage method
    public void saveEmployee(Connection conn) throws SQLException {
        String sql = "{call SAVE_EMPLOYEE(?)}";
        try (CallableStatement cs = conn.prepareCall(sql)) {
            // Create Employee object
            Employee emp = new Employee();
            emp.id = 1001;
            emp.name = "John Doe";
            emp.nName = "John";
            emp.active = true;
            emp.level = 3;
            emp.dept = 101;
            emp.age = 30;
            emp.empNo = 1234567890L;
            emp.bonusRate = 0.15f;
            emp.salary = 75000.0;
            emp.bonus = new BigDecimal("5000.00");
            emp.photo = new byte[]{0x01, 0x02, 0x03};
            emp.hireDate = new Date();
            emp.startTime = Time.valueOf("09:00:00");
            emp.lastLogin = new Timestamp(System.currentTimeMillis());
            emp.metadata = new HashMap<>();
            emp.skills = conn.createArrayOf("VARCHAR", new String[]{"Java", "SQL", "Spring"});
            emp.resume = conn.createBlob();
            emp.resume.setBytes(1, "Resume content".getBytes());
            emp.bio = conn.createClob();
            emp.bio.setString(1, "Employee biography");

            // Create STRUCT
            Struct empStruct = conn.createStruct("EMPLOYEE_TYPE",
                    new Object[]{
                            emp.id, emp.name, emp.nName, emp.active, emp.level,
                            emp.dept, emp.age, emp.empNo, emp.bonusRate, emp.salary,
                            emp.bonus, emp.photo, emp.hireDate, emp.startTime,
                            emp.lastLogin, emp.metadata, emp.skills, emp.resume,
                            emp.bio
                    });

            cs.setObject(1, empStruct);
            cs.execute();
        }
    }
}
```


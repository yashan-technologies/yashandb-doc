## 描述

SQLOutput用于参数绑定过程中把用户自定义的java类型的对象映射成数据库中的UDT类型。

`java.sql.SQLOutput`是JDBC规范中用于写入数据库自定义类型字段值的核心接口。当实现`SQLData`接口的Java类执行`writeSQL()`方法时，JDBC会自动创建`SQLOutput`实例，通过该实例的写入方法，按顺序将Java对象的属性值写入到数据库自定义类型（如`STRUCT`、复合类型）的对应字段中。


详细接口说明请参考[JDBC官方网站](https://docs.oracle.com/en/java/javase/17/docs/api/java.sql/java/sql/SQLOutput.html)。

## 方法

`java.sql.SQLOutput`包含的方法如下：

| 方法|  返回类型| 备注|
|----------------------------------------------------|------|------------------|
| writeString(String) throws SQLException            | void | 入参类型：String      |
| writeNString(String) throws SQLException           | void | 入参类型：String      |
| writeBoolean(boolean) throws SQLException          | void | 入参类型：boolean     |
| writeByte(byte) throws SQLException                | void | 入参类型：byte        |
| writeShort(short) throws SQLException              | void | 入参类型：short       |
| writeInt(int) throws SQLException                  | void | 入参类型：int         |
| writeLong(long) throws SQLException                | void | 入参类型：long        |
| writeFloat(float) throws SQLException              | void | 入参类型：float       |
| writeDouble(double) throws SQLException            | void | 入参类型：double      |
| writeBigDecimal(BigDecimal) throws SQLException    | void | 入参类型：BigDecimal  |
| writeBytes(byte[]) throws SQLException             | void | 入参类型：byte[]      |
| writeDate(Date) throws SQLException                | void | 入参类型：Date        |
| writeTime(Time) throws SQLException                | void | 入参类型：Time        |
| writeTimestamp(TImeStamp) throws SQLException      | void | 入参类型：TImeStamp   |
| writeObject(Object) throws SQLException            | void | 入参类型：Object      |
| writeObject(Class<T> type) throws SQLException     | void | 入参类型：Class       |
| writeArray(Array) throws SQLException              | void | 入参类型：Array       |
| writeRowId(RowID) throws SQLException              | void | 入参类型：RowID       |
| writeBlob(Blob) throws SQLException                | void | 入参类型：Blob        |
| writeClob(Clob) throws SQLException                | void | 入参类型：Clob        |
| writeNClob(NClob) throws SQLException              | void | 入参类型：NClob       |
| writeSQLXML(SQLXML) throws SQLException            | void | 入参类型：SQLXML      |
| writeCharacterStream(Reader) throws SQLException   | void | 入参类型：Reader      |
| writeAsciiStream(InputStream) throws SQLException  | void | 入参类型：InputStream |
| writeBinaryStream(InputStream) throws SQLException | void | 入参类型：InputStream |

## 示例

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

    // Getter和Setter
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
        // 基础类型
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

        // 大数值类型
        stream.writeBigDecimal(bonus != null ? bonus : BigDecimal.ZERO);

        // 二进制数据
        stream.writeBytes(photo != null ? photo : new byte[0]);

        // 日期和时间
        stream.writeDate(hireDate);
        stream.writeTime(startTime);
        stream.writeTimestamp(lastLogin != null ? lastLogin : new Timestamp(System.currentTimeMillis()));

        // 对象
        stream.writeObject(metadata);

        // 数组
        stream.writeArray(skills);

        // 大对象
        stream.writeBlob(resume);
        stream.writeClob(bio);
    }

    // 使用方法
    public void saveEmployee(Connection conn) throws SQLException {
        String sql = "{call SAVE_EMPLOYEE(?)}";
        try (CallableStatement cs = conn.prepareCall(sql)) {
            // 创建 Employee 对象
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

            // 创建 STRUCT
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


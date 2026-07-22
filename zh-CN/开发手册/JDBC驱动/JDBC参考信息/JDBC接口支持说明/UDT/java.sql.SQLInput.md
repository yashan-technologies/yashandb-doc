## 描述

SQLInput用于查询过程中把数据库中UDT类型映射成用户自定义的java类型。

`java.sql.SQLInput`是JDBC规范中用于读取数据库自定义类型（UDT）字段值的核心接口，它是`SQLData`接口的配套工具，当实现`SQLData`接口的Java类执行`readSQL()`方法时，JDBC会自动创建`SQLInput`实例，通过该实例的读取方法（如`readInt()`、`readString()`），按顺序读取数据库自定义类型（如`STRUCT`、复合类型）中的每个字段值，并赋值给Java对象的对应属性。


详细接口说明请参考[JDBC官方网站](https://docs.oracle.com/en/java/javase/17/docs/api/java.sql/java/sql/SQLInput.html)。

## 方法

`java.sql.SQLInput`包含的方法如下：

| 方法|  返回类型|
|-----------------------------------------------|-------------|
| readString() throws SQLException              | String      |
| readNString() throws SQLException             | String      |
| readBoolean() throws SQLException             | boolean     |
| readByte() throws SQLException                | byte        |
| readShort() throws SQLException               | short       |
| readInt() throws SQLException                 | int         |
| readLong()  throws SQLException               | long        |
| readFloat() throws SQLException               | float       |
| readDouble() throws SQLException              | double      |
| readBigDecimal() throws SQLException          | BigDecimal  |
| readBytes() throws SQLException               | byte[]      |
| readDate() throws SQLException                | Date        |
| readTime() throws SQLException                | Time        |
| readTimestamp() throws SQLException           | Timestamp   |
| readBlob() throws SQLException                | Blob        |
| readClob() throws SQLException                | Clob        |
| readNClob() throws SQLException               | NClob       |
| readSQLXML() throws SQLException              | SQLXML      |
| readCharacterStream() throws SQLException     | Reader      |
| readAsciiStream() throws SQLException         | InputStream |
| readBinaryStream() throws SQLException        | InputStream |
| readObject() throws SQLException              | Object      |
| readObject(Class<T> type) throws SQLException | <T> T       |
| readArray() throws SQLException               | Array       |
| readRowId() throws SQLException               | RowId       |
| wasNull()                                     | boolean     |

## 示例

```java
package quickstart;

import java.io.InputStream;
import java.io.Reader;
import java.math.BigDecimal;
import java.sql.Array;
import java.sql.Blob;
import java.sql.Clob;
import java.sql.Date;
import java.sql.NClob;
import java.sql.RowId;
import java.sql.SQLData;
import java.sql.SQLException;
import java.sql.SQLInput;
import java.sql.SQLOutput;
import java.sql.Time;
import java.sql.Timestamp;
import java.util.Map;

// SQLData 实现示例
public class CompleteEmployee implements SQLData {
    // 基础信息
    private int employeeId;
    private String firstName;
    private String lastName;
    private String nStringName;
    private boolean active;

    // 数值类型
    private byte securityLevel;
    private short departmentCode;
    private int age;
    private long employeeNumber;
    private float bonusRate;
    private double salary;
    private BigDecimal annualBonus;

    // 二进制数据
    private byte[] photo;

    // 日期和时间
    private Date hireDate;
    private Time startTime;
    private Timestamp lastLogin;

    // 大对象
    private Blob resume;
    private Clob biography;
    private NClob multilingualBio;
    private SQLXML configXml;

    // 复杂类型
    private Array skills;
    private RowId rowId;
    private Map<String, Object> metadata;

    // Getter和Setter
    public int getEmployeeId() { return employeeId; }
    public void setEmployeeId(int id) { this.employeeId = id; }
    public String getFirstName() { return firstName; }
    public void setFirstName(String name) { this.firstName = name; }

    // SQLData接口实现
    @Override
    public String getSQLTypeName() throws SQLException {
        return "EMPLOYEE_TYPE";
    }

    @Override
    public void readSQL(SQLInput stream, String typeName) throws SQLException {
        // 1. 读取基础信息
        employeeId = stream.readInt();
        firstName = stream.readString();
        lastName = stream.readString();
        nStringName = stream.readNString();
        active = stream.readBoolean();

        // 检查NULL值
        if (stream.wasNull()) {
            active = true;
        }

        // 2. 读取数值类型
        securityLevel = stream.readByte();
        departmentCode = stream.readShort();
        age = stream.readInt();
        employeeNumber = stream.readLong();
        bonusRate = stream.readFloat();
        salary = stream.readDouble();
        annualBonus = stream.readBigDecimal();

        // 3. 读取二进制数据
        photo = stream.readBytes();

        // 4. 读取日期和时间
        hireDate = stream.readDate();
        startTime = stream.readTime();
        lastLogin = stream.readTimestamp();

        // 5. 读取大对象
        resume = stream.readBlob();
        biography = stream.readClob();
        multilingualBio = stream.readNClob();
        configXml = stream.readSQLXML();
        
        // 6. 读取流
        try (Reader charStream = stream.readCharacterStream()) {
            // 处理字符流
        }

        try (InputStream asciiStream = stream.readAsciiStream()) {
            // 处理 ASCII 流
        }

        try (InputStream binaryStream = stream.readBinaryStream()) {
            // 处理二进制流
        }

        // 7. 读取对象
        Object rawObject = stream.readObject();
        if (rawObject instanceof Map) {
            metadata = (Map<String, Object>) rawObject;
        }

        // 使用泛型方法读取对象
        Map<String, Object> typedMetadata = stream.readObject(Map.class);

        // 8. 读取数组
        skills = stream.readArray();

        // 9. 读取行 ID
        rowId = stream.readRowId();

        // 最终检查
        if (stream.wasNull()) {
            System.out.println("Last read value was NULL");
        }
    }

    @Override
    public void writeSQL(SQLOutput stream) throws SQLException {
        // 写入数据到 SQLOutput
        stream.writeInt(employeeId);
        stream.writeString(firstName);
        stream.writeString(lastName);
        stream.writeNString(nStringName);
        stream.writeBoolean(active);
        stream.writeByte(securityLevel);
        stream.writeShort(departmentCode);
        stream.writeInt(age);
        stream.writeLong(employeeNumber);
        stream.writeFloat(bonusRate);
        stream.writeDouble(salary);
        stream.writeBigDecimal(annualBonus);
        stream.writeBytes(photo);
        stream.writeDate(hireDate);
        stream.writeTime(startTime);
        stream.writeTimestamp(lastLogin);
        stream.writeBlob(resume);
        stream.writeClob(biography);
        stream.writeNClob(multilingualBio);
        stream.writeSQLXML(configXml);
        stream.writeObject(metadata);
        stream.writeArray(skills);
        stream.writeRowId(rowId);
    }
}
```


## Description

The SQLInput interface represents that it is used to map UDT types from the database to user-defined Java types during query processing.

`java.sql.SQLInput` represents that it is the core interface in the JDBC specification for reading field values of database User-Defined Types (UDTs). It serves as a supporting utility for the `SQLData` interface. When a Java class that implements the `SQLData` interface executes the `readSQL()` method, the JDBC driver automatically creates an `SQLInput` instance. Developers can use the reading methods of this instance (such as `readInt()`, `readString()`) to read the value of each field in a database user-defined type (such as `STRUCT`, composite type) in sequence and assign the values to the corresponding attributes of a Java object.


Detailed interface specifications, please refer to [The JDBC Official Website](https://docs.oracle.com/en/java/javase/17/docs/api/java.sql/java/sql/SQLInput.html).

## Method

The methods included in `java.sql.SQLInput` are as follows:

|Method |Return Type |
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

## Example

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

// Complete SQLData implementation example
public class CompleteEmployee implements SQLData {
    // Basic information
    private int employeeId;
    private String firstName;
    private String lastName;
    private String nStringName;
    private boolean active;

    // Numeric types
    private byte securityLevel;
    private short departmentCode;
    private int age;
    private long employeeNumber;
    private float bonusRate;
    private double salary;
    private BigDecimal annualBonus;

    // Binary data
    private byte[] photo;

    // Date and time
    private Date hireDate;
    private Time startTime;
    private Timestamp lastLogin;

    // Large objects
    private Blob resume;
    private Clob biography;
    private NClob multilingualBio;
    private SQLXML configXml;

    // Complex types
    private Array skills;
    private RowId rowId;
    private Map<String, Object> metadata;

    // Getters and Setters
    public int getEmployeeId() { return employeeId; }
    public void setEmployeeId(int id) { this.employeeId = id; }
    public String getFirstName() { return firstName; }
    public void setFirstName(String name) { this.firstName = name; }

    // SQLData interface implementation
    @Override
    public String getSQLTypeName() throws SQLException {
        return "EMPLOYEE_TYPE";
    }

    @Override
    public void readSQL(SQLInput stream, String typeName) throws SQLException {
        // 1. Read basic information
        employeeId = stream.readInt();
        firstName = stream.readString();
        lastName = stream.readString();
        nStringName = stream.readNString();
        active = stream.readBoolean();

        // Check for NULL values
        if (stream.wasNull()) {
            active = true;
        }

        // 2. Read numeric types
        securityLevel = stream.readByte();
        departmentCode = stream.readShort();
        age = stream.readInt();
        employeeNumber = stream.readLong();
        bonusRate = stream.readFloat();
        salary = stream.readDouble();
        annualBonus = stream.readBigDecimal();

        // 3. Read binary data
        photo = stream.readBytes();

        // 4. Read date and time
        hireDate = stream.readDate();
        startTime = stream.readTime();
        lastLogin = stream.readTimestamp();

        // 5. Read large objects
        resume = stream.readBlob();
        biography = stream.readClob();
        multilingualBio = stream.readNClob();
        configXml = stream.readSQLXML();
        
        // 6. Read streams
        try (Reader charStream = stream.readCharacterStream()) {
            // Process character stream
        }

        try (InputStream asciiStream = stream.readAsciiStream()) {
            // Process ASCII stream
        }

        try (InputStream binaryStream = stream.readBinaryStream()) {
            // Process binary stream
        }

        // 7. Read objects
        Object rawObject = stream.readObject();
        if (rawObject instanceof Map) {
            metadata = (Map<String, Object>) rawObject;
        }

        // Read object using generic method
        Map<String, Object> typedMetadata = stream.readObject(Map.class);

        // 8. Read array
        skills = stream.readArray();

        // 9. Read row ID
        rowId = stream.readRowId();

        // Final check
        if (stream.wasNull()) {
            System.out.println("Last read value was NULL");
        }
    }

    @Override
    public void writeSQL(SQLOutput stream) throws SQLException {
        // Write data to SQLOutput
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


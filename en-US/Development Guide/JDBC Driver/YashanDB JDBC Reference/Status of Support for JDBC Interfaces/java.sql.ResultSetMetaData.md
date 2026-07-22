## Description

`java.sql.ResultSetMetaData` represents that it is the result set metadata interface defined in the JDBC specification. "Metadata" means data that describes data. It does not store the actual row data from the query, but describes the structural characteristics of `ResultSet`. Its core functions are as follows:


- Get the total number of columns, column names, and column labels in the result set;
- Query properties such as the SQL type, Java type, precision, and scale of each column;
- Determine whether a column is nullable, auto-increment, and other attributes;
- Suitable for dynamic query scenarios (dynamically parsing column information when the column structure of the query result is unknown).

Detailed interface specifications, please refer to [The JDBC Official Website](https://docs.oracle.com/en/java/javase/17/docs/api/java.sql/java/sql/ResultSetMetaData.html).

## Method

The YashanDB JDBC driver has the following methods for the ResultSetMetaData interface:

|Method |Return Type |
|----------------------------------|----------|
| getColumnCount()                 | int      |
| isAutoIncrement(int column)      | boolean  |
| isCaseSensitive(int column)      | boolean  |
| isSearchable(int column)         | boolean  |
| isCurrency(int column)           | boolean  |
| isNullable(int column)           | int      |
| isSigned(int column)             | boolean  |
| getColumnLabel(int column)       | String   |
| getColumnName(int column)        | String   |
| getPrecision(int column)         | int      |
| getScale(int column)             | int      |
| getColumnDisplaySize(int column) | int      |
| getColumnType(int column)        | int      |
| getColumnTypeName(int column)    | String   |
| isReadOnly(int column)           | boolean  |
| isWritable(int column)           | boolean  |
| isDefinitelyWritable(int column) | boolean  |
| getColumnClassName(int column)   | String   |

## Example

```java
package quickstart;

import java.sql.ResultSet;
import java.sql.ResultSetMetaData;
import java.sql.SQLException;
import java.util.HashMap;
import java.util.Map;

public class ResultSetMetaDataExample {

    // Print ResultSet structure
    public void printResultSetStructure(ResultSet rs) throws SQLException {
        ResultSetMetaData meta = rs.getMetaData();
        int cols = meta.getColumnCount();

        for (int i = 1; i <= cols; i++) {
            String name = meta.getColumnName(i);
            String type = meta.getColumnTypeName(i);
            int size = meta.getPrecision(i);
            System.out.println(name + " (" + type + "(" + size + "))");
        }
    }

    // Automatically map to Map
    public Map<String, Object> toMap(ResultSet rs) throws SQLException {
        Map<String, Object> map = new HashMap<>();
        ResultSetMetaData meta = rs.getMetaData();

        for (int i = 1; i <= meta.getColumnCount(); i++) {
            String colName = meta.getColumnName(i);
            map.put(colName, rs.getObject(i));
        }
        return map;
    }

    // Validate query result
    public void validateResultSet(ResultSet rs) throws SQLException {
        ResultSetMetaData meta = rs.getMetaData();

        for (int i = 1; i <= meta.getColumnCount(); i++) {
            if (meta.isNullable(i) == ResultSetMetaData.columnNoNulls) {
                Object value = rs.getObject(i);
                if (rs.wasNull()) {
                    throw new SQLException("Column " + meta.getColumnName(i) + " cannot be NULL");
                }
            }
        }
    }

    // Generate CSV header
    public String generateCSVHeader(ResultSet rs) throws SQLException {
        ResultSetMetaData meta = rs.getMetaData();
        StringBuilder header = new StringBuilder();

        for (int i = 1; i <= meta.getColumnCount(); i++) {
            if (i > 1) header.append(",");
            header.append(meta.getColumnLabel(i));
        }
        return header.toString();
    }

    // Type-safe reading
    public Object getValueSafely(ResultSet rs, int columnIndex) throws SQLException {
        ResultSetMetaData meta = rs.getMetaData();
        String className = meta.getColumnClassName(columnIndex);

        if ("java.lang.String".equals(className)) {
            return rs.getString(columnIndex);
        } else if ("java.lang.Integer".equals(className)) {
            return rs.getInt(columnIndex);
        } else if ("java.math.BigDecimal".equals(className)) {
            return rs.getBigDecimal(columnIndex);
        }
        return rs.getObject(columnIndex);
    }
}
```


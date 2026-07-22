## Description

The ParameterMetadata interface represents that it is used to obtain type and attribute information of the bound parameters in PreparedStatement.

`java.sql.ParameterMetaData` is the core interface for retrieving parameter metadata of precompiled SQL statements, which can dynamically query attributes such as the number, type, and nullability of placeholders.

Detailed interface specifications, please refer to [The JDBC Official Website](https://docs.oracle.com/en/java/javase/17/docs/api/java.sql/java/sql/ParameterMetaData.html).

## Method

The YashanDB JDBC driver has the following methods for the ParameterMetaData interface:

|Method |Return Type |
|---------------------------------|--------|
| getParameterCount()             | int    |

## Example

```java
package quickstart;

import java.math.BigDecimal;
import java.sql.ParameterMetaData;
import java.sql.PreparedStatement;
import java.sql.SQLException;
import java.sql.Types;

public class ParameterMetaDataExample {

    // View all parameter information
    public void viewAllParameters(PreparedStatement ps) throws SQLException {
        ParameterMetaData meta = ps.getParameterMetaData();
        int count = meta.getParameterCount();

        for (int i = 1; i <= count; i++) {
            int type = meta.getParameterType(i);
            String typeName = meta.getParameterTypeName(i);
            int precision = meta.getPrecision(i);
            int scale = meta.getScale(i);

            System.out.printf("Parameter %d: Type = %s, Precision = %d, Scale = %d%n",
                i, typeName, precision, scale);
        }
    }

    // Automatically set parameters based on metadata
    public void autoSetParameters(PreparedStatement ps) throws SQLException {
        ParameterMetaData meta = ps.getParameterMetaData();

        for (int i = 1; i <= meta.getParameterCount(); i++) {
            int type = meta.getParameterType(i);

            if (type == Types.INTEGER) {
                ps.setInt(i, 0);
            } else if (type == Types.VARCHAR) {
                ps.setString(i, "");
            } else if (type == Types.DECIMAL) {
                ps.setBigDecimal(i, BigDecimal.ZERO);
            }
        }
    }

    // Validate parameter settings
    public void validateParameters(PreparedStatement ps, Object... values)
            throws SQLException {
        ParameterMetaData meta = ps.getParameterMetaData();

        if (meta.getParameterCount() != values.length) {
            throw new SQLException("Parameter count mismatch");
        }
    }
}
```


## 描述

ParameterMetadata接口用于获取PreparedStatement中绑定参数的类型与属性信息。

`java.sql.ParameterMetaData`是获取预编译SQL语句参数元数据的核心接口，能动态查询占位符的数量、类型、是否可为空等属性。

详细接口说明请参考[JDBC官方网站](https://docs.oracle.com/en/java/javase/17/docs/api/java.sql/java/sql/ParameterMetaData.html)。

## 方法

YashanDB JDBC驱动对ParameterMetaData接口有如下方法：

| 方法|  返回类型|
|---------------------------------|--------|
| getParameterCount()             | int    |
| getPrecision(int param)         | int    |
| getScale(int param)             | int    |
| getParameterType(int param)     | int    |
| getParameterTypeName(int param) | String |

## 示例

```java
package quickstart;

import java.math.BigDecimal;
import java.sql.ParameterMetaData;
import java.sql.PreparedStatement;
import java.sql.SQLException;
import java.sql.Types;

public class ParameterMetaDataExample {

    // 查看所有参数信息
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

    // 根据元数据自动设置参数
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

    // 验证参数设置
    public void validateParameters(PreparedStatement ps, Object... values)
            throws SQLException {
        ParameterMetaData meta = ps.getParameterMetaData();

        if (meta.getParameterCount() != values.length) {
            throw new SQLException("Parameter count mismatch");
        }
    }
}
```


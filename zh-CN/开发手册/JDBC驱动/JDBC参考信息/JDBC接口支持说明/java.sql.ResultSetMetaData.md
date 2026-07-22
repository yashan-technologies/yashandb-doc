## 描述

`java.sql.ResultSetMetaData`是JDBC规范中定义的结果集元数据接口，元数据即描述数据的数据，它不存储查询的具体行数据，而是描述`ResultSet`的结构特征，核心作用是：


- 获取结果集中列的总数、列名、列标签。
- 查询每列的SQL类型、Java类型、精度、刻度等属性。
- 判断列是否可为空、是否为自动递增列等特性。
- 适用于动态查询场景（不知道查询结果的列结构时，动态解析列信息）。

详细接口说明请参考[JDBC官方网站](https://docs.oracle.com/en/java/javase/17/docs/api/java.sql/java/sql/ResultSetMetaData.html)。

## 方法

YashanDB JDBC驱动对ResultSetMetaData接口有如下方法：

| 方法|  返回类型|
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

## 示例

```java
package quickstart;

import java.sql.ResultSet;
import java.sql.ResultSetMetaData;
import java.sql.SQLException;
import java.util.HashMap;
import java.util.Map;

public class ResultSetMetaDataExample {

    // 打印 ResultSet 结构
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

    // 自动映射到 Map
    public Map<String, Object> toMap(ResultSet rs) throws SQLException {
        Map<String, Object> map = new HashMap<>();
        ResultSetMetaData meta = rs.getMetaData();

        for (int i = 1; i <= meta.getColumnCount(); i++) {
            String colName = meta.getColumnName(i);
            map.put(colName, rs.getObject(i));
        }
        return map;
    }

    // 验证查询结果
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

    // 生成 CSV 表头
    public String generateCSVHeader(ResultSet rs) throws SQLException {
        ResultSetMetaData meta = rs.getMetaData();
        StringBuilder header = new StringBuilder();

        for (int i = 1; i <= meta.getColumnCount(); i++) {
            if (i > 1) header.append(",");
            header.append(meta.getColumnLabel(i));
        }
        return header.toString();
    }

    // 类型安全读取
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


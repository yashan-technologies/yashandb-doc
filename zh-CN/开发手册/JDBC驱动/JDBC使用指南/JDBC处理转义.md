## 转义概述
YashanDB JDBC允许在应用程序中书写SQL语句时，对日期时间型的变量进行特殊转义处理。

YashanDB JDBC允许在应用程序中书写SQL语句时，按如下格式输写日期时间型的变量，并在传递服务端执行之前将它们转义为YashanDB服务端可接受的日期时间格式。

```sql
-- 以下d/s/ts不区分大小写

-- 日期
{d 'yyyy-mm-dd'}
{d 'yyyymmdd'} 
{d 'yyyy/mm/dd'} 
-- 时间
{t 'hh:mm:ss'}
-- 时间戳
{ts 'yyyy-mm-dd hh:mm:ss.fff'}
{ts 'yyyy/mm/dd hh:mm:ss.fff'}
```

### 日期变量转义

对于日期类型变量，YashanDB JDBC将引号中的字符串作为TO_DATE函数的参数进行转义，例如`select {d '2020-02-01'} from dual`被转义为`select TO_DATE('2020-02-01', 'YYYY-MM-DD') from dual`；`select {d '20200201'} from dual`被转义为`select TO_DATE('20200201', 'YYYY-MM-DD') from dual`。请注意转义后的日期格式均为`YYYY-MM-DD`。

变量为绑定参数时，支持以变量符号替换具体日期，例如`select {d ?} from dual`转义为`select TO_DATE (?, 'YYYY-MM-DD') from dual`。

此外，与JDBC标准的语法转义格式规范一致，也支持如下写法，并同样按上述规则进行转义：

```sql
select {d '2020-02-01'} from dual
select {d '2020-02-01'}/*This is a comment*/ from dual
select { d   /*This is a comment*/  '20200201'} from dual
```

### 时间变量转义

对于时间类型变量，YashanDB JDBC将引号中的字符串补齐日期（1970-01-01）作为TO_TIMESTAMP函数的参数进行转义，例如`select {t '01:59:01'} from dual`被转义为`select TO_TIMESTAMP('1970-01-01 01:59:01', 'YYYY-MM-DD HH24:MI:SS.FF') from dual`。请注意时间戳格式均为`YYYY-MM-DD HH24:MI:SS.FF`。

变量为绑定参数时，支持以变量符号替换具体时间，例如`select {t ?} from dual`转义为`select TO_TIMESTAMP('1970-01-01 '||?, 'YYYY-MM-DD') from dual`。

此外，与JDBC标准的语法转义格式规范一致，也支持如下写法，并同样按上述规则进行转义：

```sql
select {t '01:9:01'} from dual
select {t '01:9:01'}/*This is a comment*/ from dual
select { t   /*This is a comment*/  '01:9:01'} from dual
```

### 时间戳变量转义

对于时间戳类型变量，YashanDB JDBC将引号中的字符串作为TO_TIMESTAMP函数的参数进行转义，例如`select {ts '2020-02-01 01:09:01'} from dual`被转义为`select TO_TIMESTAMP('2020-02-01 01:09:01', 'YYYY-MM-DD HH24:MI:SS.FF') from dual`；`select {ts '2020/02/01 01:09:01.444'} from dual`被转义为`select TO_TIMESTAMP('2020/02/01 01:09:01.444', 'YYYY-MM-DD HH24:MI:SS.FF') from dual`。请注意转义后的时间戳格式均为`YYYY-MM-DD HH24:MI:SS.FF`。

变量为绑定参数时，支持以变量符号替换具体时间，例如`select {ts ?} from dual`转义为`select TO_TIMESTAMP(?, 'YYYY-MM-DD HH24:MI:SS.FF') from dual`。

此外，与JDBC标准的语法转义格式规范一致，也支持如下写法，并同样按上述规则进行转义：

```sql
select {ts '2020-02-01 01:99:01'} from dual
select {ts '2020-02-01 01:99:01'}/*This is a comment*/ from dual
select { ts   /*This is a comment*/  '2020-02-01 01:99:01'} from dual
```

### FN转义

JDBC FN转义是JDBC规范提供的一种标准语法，用于统一不同数据库系统中内置标量函数的调用方式，允许用户编写与具体数据库无关的函数调用代码。JDBC驱动程序在运行时负责将其转换为YashanDB数据库所支持的原生函数语法，从而实现跨数据库的SQL函数调用兼容性，提升代码的可移植性和维护性。

| 语法| 描述|
| --------------------------------- | ----------------------------------------------------------- |
| {fn function_name(arg1, arg2, …)} | function_name目前仅支持LENGTH函数                           |

```java
// 使用FN转义语法调用LENGTH函数
SELECT {fn LENGTH(column_name)} AS str_length FROM table_name;
// 具体示例：获取员工姓名长度
SELECT employee_name, {fn LENGTH(employee_name)} AS name_length FROM employees;
```

### CALL转义

JDBC Call转义语法是JDBC规范中用于调用存储过程或函数的标准语法格式。它允许用户通过统一的`{call ...}`格式编写调用语句，并由JDBC驱动程序自动适配为YashanDB数据库所支持的原生调用语法，从而实现跨数据库兼容的存储过程/函数调用。

| 语法| 说明|
| ------------------------------- | ---------------------------------------------- |
| {call procedure_name(?, ?, …)}  | 调用存储过程                                   |
| {?=call function_name(?, ?, …)} | 调用函数并取返回值                             |

- procedure_name/function_name：数据库中已存在的存储过程或函数。
- ?：占位符（参数），可以使用`PreparedStatement.setXxx()`。
- {?=call …}用来获取返回值（仅适用于有返回值的函数），需要使用`registerOutParameter()`注册输出参数。

```java
// 调用无返回值的存储过程
String sql = "{call update_employee_salary(?, ?)}";   // 参数：员工ID、新薪资
PreparedStatement ps = conn.prepareStatement(sql);
ps.setInt(1, 1001);
ps.setBigDecimal(2, new BigDecimal("75000.00"));
ps.executeUpdate();   // 不返回 ResultSet

// 调用有返回值的函数
String sql = "{?=call get_employee_name(?)}"; // 返回员工姓名
CallableStatement cs = conn.prepareCall(sql);
cs.registerOutParameter(1, Types.VARCHAR);   // 注册返回值
cs.setInt(2, 1001);
cs.execute();
String name = cs.getString(1);   // 取得函数返回值
```

## 转义实践

### 日期变量转义

```java
package quickstart;

import java.sql.*;
import java.util.Properties;

public class DateVariableEscapingExample {
    public static void main(String[] args) {
        // 数据库连接参数
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        // 加载驱动
        try {
            Class.forName("com.yashandb.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            return;
        }

        try (Connection conn = DriverManager.getConnection(url, info)) {

            // 使用日期变量查询
            // 使用YashanDB原生日期语法
            String sql = "SELECT DATE '2020-02-01' FROM DUAL";

            try (Statement stmt = conn.createStatement();
                 ResultSet rs = stmt.executeQuery(sql)) {

                if (rs.next()) {
                    String currentDate = rs.getString(1);
                    System.out.println("Current date: " + currentDate);

                    // 验证转义效果
                    System.out.println("Server properly escaped date variable");
                }
            }

        } catch (SQLException e) {
            System.out.println("Error executing date query: " + e.getMessage());
            e.printStackTrace();
        }
    }
}
```

### 时间变量转义

```java
package quickstart;

import java.sql.*;
import java.util.Properties;

public class TimeVariableEscapingExample {
    public static void main(String[] args) {
        // 数据库连接参数
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        // 加载驱动
        try {
            Class.forName("com.yashandb.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            return;
        }

        try (Connection conn = DriverManager.getConnection(url, info)) {

            // 使用时间变量查询
            // 直接使用字符串时间或从dual查询
            String sql = "SELECT TO_TIMESTAMP('14:30:59', 'HH24:MI:SS') FROM DUAL";

            try (Statement stmt = conn.createStatement();
                 ResultSet rs = stmt.executeQuery(sql)) {

                if (rs.next()) {
                    String currentTime = rs.getString(1);
                    System.out.println("Current time: " + currentTime);
                }
            }

        } catch (SQLException e) {
            System.out.println("Error executing time query: " + e.getMessage());
            e.printStackTrace();
        }
    }
}
```

### 时间戳变量转义

```java
package quickstart;

import java.sql.*;
import java.util.Properties;

public class TimestampVariableEscapingExample {
    public static void main(String[] args) {
        // 数据库连接参数
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        // 加载驱动
        try {
            Class.forName("com.yashandb.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            return;
        }

        try (Connection conn = DriverManager.getConnection(url, info)) {

            // 使用时间戳变量查询
            // 使用YashanDB原生时间戳语法
            String sql = "SELECT TIMESTAMP '2020-02-01 12:00:00' FROM DUAL";

            try (Statement stmt = conn.createStatement();
                 ResultSet rs = stmt.executeQuery(sql)) {

                if (rs.next()) {
                    String currentTimestamp = rs.getString(1);
                    System.out.println("Current timestamp: " + currentTimestamp);
                }
            }

        } catch (SQLException e) {
            System.out.println("Error executing timestamp query: " + e.getMessage());
            e.printStackTrace();
        }
    }
}
```

### 使用函数转义

```java
package quickstart;

import java.sql.*;
import java.util.Properties;

public class FunctionEscapingExample {
    public static void main(String[] args) {
        // 数据库连接参数
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        // 加载驱动
        try {
            Class.forName("com.yashandb.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            return;
        }

        try (Connection conn = DriverManager.getConnection(url, info)) {

            // 使用TO_DATE函数转义
            String sql = "SELECT TO_DATE('2025-12-25', 'YYYY-MM-DD') FROM dual";

            try (Statement stmt = conn.createStatement();
                 ResultSet rs = stmt.executeQuery(sql)) {

                if (rs.next()) {
                    String escapedDate = rs.getString(1);
                    System.out.println("Escaped date: " + escapedDate);

                    // 验证转义格式
                    System.out.println("Date format: 2025-12-25");
                }
            }

        } catch (SQLException e) {
            System.out.println("Error using TO_DATE: " + e.getMessage());
            e.printStackTrace();
        }
    }
}
```

### 使用TO_TIMESTAMP函数转义

```java
package quickstart;

import java.sql.*;
import java.util.Properties;

public class TimestampFunctionEscapingExample {
    public static void main(String[] args) {
        // 数据库连接参数
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        // 加载驱动
        try {
            Class.forName("com.yashandb.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            return;
        }

        try (Connection conn = DriverManager.getConnection(url, info)) {

            // 使用TO_TIMESTAMP函数转义
            String sql = "SELECT TO_TIMESTAMP('2025-06-15 14:30:25', 'YYYY-MM-DD HH24:MI:SS.FF') FROM dual";

            try (Statement stmt = conn.createStatement();
                 ResultSet rs = stmt.executeQuery(sql)) {

                if (rs.next()) {
                    String escapedTimestamp = rs.getString(1);
                    System.out.println("Escaped timestamp: " + escapedTimestamp);

                    // 验证转义格式
                    System.out.println("Timestamp format: 2025-06-15 14:30:25");
                }
            }

        } catch (SQLException e) {
            System.out.println("Error using TO_TIMESTAMP: " + e.getMessage());
            e.printStackTrace();
        }
    }
}
```

### 注释语法支持

```java
package quickstart;

import java.sql.*;
import java.util.Properties;

public class CommentSyntaxExample {
    public static void main(String[] args) {
        // 数据库连接参数
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        // 加载驱动
        try {
            Class.forName("com.yashandb.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            return;
        }

        try (Connection conn = DriverManager.getConnection(url, info)) {

            // 使用带注释的SQL语句
            // 使用YashanDB原生日期语法
            String sql = "SELECT DATE '2020-02-01' /*This is a comment*/ FROM DUAL";

            try (Statement stmt = conn.createStatement();
                 ResultSet rs = stmt.executeQuery(sql)) {

                if (rs.next()) {
                    String date = rs.getString(1);
                    System.out.println("Date with comment: " + date);
                }
            }

        } catch (SQLException e) {
            System.out.println("Error executing query with comment: " + e.getMessage());
            e.printStackTrace();
        }
    }
}
```



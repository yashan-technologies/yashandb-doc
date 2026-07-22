## Escaping Overview
YashanDB JDBC allows special escaping processing for date-time type variables when writing SQL statements in applications.

When writing SQL statements in applications supported by YashanDB JDBC, date-time variables can be written in the following format, and before passing them to the server for execution, they will be escaped to the date-time format acceptable by the YashanDB server.

```sql
-- The following d/s/ts are case-insensitive.

-- Date
{d 'yyyy-mm-dd'}
{d 'yyyymmdd'} 
{d 'yyyy/mm/dd'} 
-- Time
{t 'hh:mm:ss'}
-- Timestamp
{ts 'yyyy-mm-dd hh:mm:ss.fff'}
{ts 'yyyy/mm/dd hh:mm:ss.fff'}
```

### Date Variable Escaping 

For date type variables, YashanDB JDBC escapes the string in quotes as a parameter of the TO_DATE function. For example, `select {d'2020-02-01'} from dual` is escaped to `select TO_DATE ('2020-02-01', 'YYYY-MM-DD') from dual`; `select {d'20200201'} from dual` is escaped to `select TO_DATE ('20200201', 'YYYY-MM-DD') from dual`. Please note that the escaped date format is always `YYYY-MM-DD`. 

When the variable is a bind parameter, the rule is to escape, for example, `select {d ?} from dual` to `select TO_DATE (?, 'YYYY-MM-DD') from dual`. 

In addition, consistent with the JDBC standard syntax escape format specification, the following writing style is also supported and escaped according to the above rules:

```sql
select {d '2020-02-01'} from dual
select {d '2020-02-01'}/*This is a comment*/ from dual
select { d   /*This is a comment*/  '20200201'} from dual
```

### Time Variable Escaping 

For time type variables, YashanDB JDBC pads the string in quotes with a date (1970-01-01) and escapes it as a parameter of the TO_TIMESTAMP function. For example, `select {t'01:59:01'} from dual` is escaped to `select TO_TIMESTAMP('1970-01-01 01:59:01', 'YYYY-MM-DD HH24:MI:SS.FF') from dual`. Please note that the timestamp format is always `YYYY-MM-DD HH24:MI:SS.FF`. 

When the variable is a bind parameter, the rule is to escape, for example, `select {t ?} from dual` to `select TO_TIMESTAMP('1970-01-01 '||?, 'YYYY-MM-DD') from dual`. 

In addition, consistent with the JDBC standard syntax escape format specification, the following writing style is also supported and escaped according to the above rules:

```sql
select {t '01:9:01'} from dual
select {t '01:9:01'}/*This is a comment*/ from dual
select { t   /*This is a comment*/  '01:9:01'} from dual
```

### Timestamp Variable Escaping 

For timestamp type variables, YashanDB JDBC escapes the string in quotes as a parameter of the TO_TIMESTAMP function. For example, `select {ts '2020-02-01 01:09:01'} from dual` is escaped to `select TO_TIMESTAMP('2020-02-01 01:09:01', 'YYYY-MM-DD HH24:MI:SS.FF') from dual`; `select {ts '2020/02/01 01:09:01.444'} from dual` is escaped to `select TO_TIMESTAMP('2020/02/01 01:09:01.444', 'YYYY-MM-DD HH24:MI:SS.FF') from dual`. Please note that the escaped timestamp format is always `YYYY-MM-DD HH24:MI:SS.FF`. 

When the variable is a bind parameter, the rule is to escape, for example, `select {ts ?} from dual` to `select TO_TIMESTAMP(?, 'YYYY-MM-DD HH24:MI:SS.FF') from dual`. 

In addition, consistent with the JDBC standard syntax escape format specification, the following writing style is also supported and escaped according to the above rules:

```sql
select {ts '2020-02-01 01:99:01'} from dual
select {ts '2020-02-01 01:99:01'}/*This is a comment*/ from dual
select { ts   /*This is a comment*/  '2020-02-01 01:99:01'} from dual
```

### FN escape

JDBC FN escape is a standard syntax provided by the JDBC specification, designed to unify the invocation of built-in scalar functions across different database systems. It allows users to write function call code that is independent of any specific database. The JDBC driver is responsible for converting it at runtime into the native function syntax supported by the YashanDB database, thereby achieving cross-database compatibility for SQL function calls and enhancing the portability and maintainability of the code.

|Syntax                   |Description                                        |
| --------------------------------- | ----------------------------------------------------------- |
| {fn function_name(arg1, arg2, …)} | function_name currently only supports the `LENGTH`function. |

```java
// Using FN escape syntax to call the LENGTH function
SELECT {fn LENGTH(column_name)} AS str_length FROM table_name;
// Specific example: Get the length of employee names
SELECT employee_name, {fn LENGTH(employee_name)} AS name_length FROM employees;
```

###  CALL escape

JDBC Call escape syntax is a standard syntactic format defined by the JDBC specification for invoking stored procedures or functions. It allows users to write call statements using the unified `{call ...}`format. The JDBC driver automatically adapts these to the native call syntax supported by the YashanDB database, thereby enabling cross-database compatible calls to stored procedures and functions.

|Syntax                 |Description                           |
| ------------------------------- | ---------------------------------------------- |
| {call procedure_name(?, ?, …)}  | Calling Stored Procedures                      |
| {?=call function_name(?, ?, …)} | Calling Functions and Retrieving Return Values |

- procedure_name/function_name: The stored procedure or function that already exists in the database.
- ?: Placeholder (parameter), which can be used with `PreparedStatement.setXxx()`.
- {?=call …}: Used to retrieve a return value (applies only to functions with a return value). It requires registering the output parameter using `registerOutParameter()`.

```java
// Calling a stored procedure without a return value
String sql = "{call update_employee_salary(?, ?)}";   // Parameters: employee ID, new salary
PreparedStatement ps = conn.prepareStatement(sql);
ps.setInt(1, 1001);
ps.setBigDecimal(2, new BigDecimal("75000.00"));
ps.executeUpdate();   // Does not return a ResultSet

// Calling a function with a return value (using Oracle as an example)
String sql = "{?=call get_employee_name(?)}"; // Returns employee name
CallableStatement cs = conn.prepareCall(sql);
cs.registerOutParameter(1, Types.VARCHAR);   // Register return value
cs.setInt(2, 1001);
cs.execute();
String name = cs.getString(1);   // Get function return value
```

## Escaping Practice

### Date Variable Escaping

```java
package quickstart;

import java.sql.*;
import java.util.Properties;

public class DateVariableEscapingExample {
    public static void main(String[] args) {
        // Database connection parameters
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        // Load driver
        try {
            Class.forName("com.yashandb.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            return;
        }

        try (Connection conn = DriverManager.getConnection(url, info)) {

            // Query using date variable
            // Use YashanDB native date syntax
            String sql = "SELECT DATE '2020-02-01' FROM DUAL";

            try (Statement stmt = conn.createStatement();
                 ResultSet rs = stmt.executeQuery(sql)) {

                if (rs.next()) {
                    String currentDate = rs.getString(1);
                    System.out.println("Current date: " + currentDate);

                    // Verify escaping effect
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

### Time Variable Escaping

```java
package quickstart;

import java.sql.*;
import java.util.Properties;

public class TimeVariableEscapingExample {
    public static void main(String[] args) {
        // Database connection parameters
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        // Load driver
        try {
            Class.forName("com.yashandb.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            return;
        }

        try (Connection conn = DriverManager.getConnection(url, info)) {

            // Query using time variable
            // Use string time directly or query from dual
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

### Timestamp Variable Escaping

```java
package quickstart;

import java.sql.*;
import java.util.Properties;

public class TimestampVariableEscapingExample {
    public static void main(String[] args) {
        // Database connection parameters
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        // Load driver
        try {
            Class.forName("com.yashandb.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            return;
        }

        try (Connection conn = DriverManager.getConnection(url, info)) {

            // Query using timestamp variable
            // Use YashanDB native timestamp syntax
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

### Function Escaping

```java
package quickstart;

import java.sql.*;
import java.util.Properties;

public class FunctionEscapingExample {
    public static void main(String[] args) {
        // Database connection parameters
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        // Load driver
        try {
            Class.forName("com.yashandb.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            return;
        }

        try (Connection conn = DriverManager.getConnection(url, info)) {

            // Use TO_DATE function for escaping
            String sql = "SELECT TO_DATE('2025-12-25', 'YYYY-MM-DD') FROM dual";

            try (Statement stmt = conn.createStatement();
                 ResultSet rs = stmt.executeQuery(sql)) {

                if (rs.next()) {
                    String escapedDate = rs.getString(1);
                    System.out.println("Escaped date: " + escapedDate);

                    // Verify escaped format
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

### Using TO_TIMESTAMP Function for Escaping

```java
package quickstart;

import java.sql.*;
import java.util.Properties;

public class TimestampFunctionEscapingExample {
    public static void main(String[] args) {
        // Database connection parameters
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        // Load driver
        try {
            Class.forName("com.yashandb.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            return;
        }

        try (Connection conn = DriverManager.getConnection(url, info)) {

            // Use TO_TIMESTAMP function for escaping
            String sql = "SELECT TO_TIMESTAMP('2025-06-15 14:30:25', 'YYYY-MM-DD HH24:MI:SS.FF') FROM dual";

            try (Statement stmt = conn.createStatement();
                 ResultSet rs = stmt.executeQuery(sql)) {

                if (rs.next()) {
                    String escapedTimestamp = rs.getString(1);
                    System.out.println("Escaped timestamp: " + escapedTimestamp);

                    // Verify escaped format
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

### Comment Syntax Support

```java
package quickstart;

import java.sql.*;
import java.util.Properties;

public class CommentSyntaxExample {
    public static void main(String[] args) {
        // Database connection parameters
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        // Load driver
        try {
            Class.forName("com.yashandb.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            return;
        }

        try (Connection conn = DriverManager.getConnection(url, info)) {

            // Use SQL statement with comments
            // Use YashanDB native date syntax
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



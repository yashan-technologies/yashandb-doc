JDBC uses the CallableStatement interface to call database stored procedures and functions.

## Basic Stored Procedure Call

### Parameterless Stored Procedure

The simplest stored procedure call is the case without parameters.

```java
package quickstart;

import java.sql.*;
import java.util.Properties;

public class SimpleProcedureCallExample {
    public static void main(String[] args) {
        // Database connection parameters
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        // Declare resource objects
        Connection conn = null;
        CallableStatement cstmt = null;
        ResultSet rs = null;

        // Load driver
        try {
            Class.forName("com.yashandb.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            return;
        }

        try {
            // Get database connection
            conn = DriverManager.getConnection(url, info);

            // First create stored procedure (if not exists)
            Statement stmt = conn.createStatement();
            try {
                stmt.execute("CREATE OR REPLACE PROCEDURE get_system_time AS " +
                    "BEGIN " +
                    "  NULL; " +
                    "END get_system_time;");
            } catch (SQLException e) {
                e.printStackTrace();
            }
            stmt.close();

            // Call parameterless stored procedure
            String procedureCall = "{call get_system_time()}";
            cstmt = conn.prepareCall(procedureCall);

            // Execute stored procedure
            cstmt.execute();

            // Get result
            rs = cstmt.getResultSet();
            if (rs != null) {
                while (rs.next()) {
                    // Get system time
                    Timestamp systemTime = rs.getTimestamp(1);
                    System.out.println("System time: " + systemTime);
                }
            }

        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            // Close resources (Order: ResultSet -> CallableStatement -> Connection)
            if (rs != null) {
                try {
                    rs.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
            if (cstmt != null) {
                try {
                    cstmt.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
            if (conn != null) {
                try {
                    conn.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```

### Stored Procedure with IN Parameters

Stored procedures can accept input parameters, with parameter values set through set methods.

```java
package quickstart;

import java.sql.*;
import java.util.Properties;

public class INParameterExample {
    public static void main(String[] args) {
        // Database connection parameters
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        // Declare resource objects
        Connection conn = null;
        CallableStatement cstmt = null;

        // Load driver
        try {
            Class.forName("com.yashandb.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            return;
        }

        try {
            // Get database connection
            conn = DriverManager.getConnection(url, info);

            // First create table and stored procedure (if not exists)
            Statement stmt = conn.createStatement();
            try {
                stmt.execute("CREATE TABLE IF NOT EXISTS employee(id INTEGER, name VARCHAR(100), department VARCHAR(50), salary DOUBLE)");
                System.out.println("Table created");
            } catch (SQLException e) {
                System.out.println("Table may already exist: " + e.getMessage());
            }

            try {
                stmt.execute("CREATE OR REPLACE PROCEDURE add_employee(" +
                    "p_id IN INTEGER, p_name IN VARCHAR, p_dept IN VARCHAR, p_salary IN DOUBLE) AS " +
                    "BEGIN " +
                    "  INSERT INTO employee(id, name, department, salary) VALUES (p_id, p_name, p_dept, p_salary); " +
                    "END add_employee;");
                System.out.println("Procedure created successfully");
            } catch (SQLException e) {
                System.out.println("Procedure may already exist or creation skipped: " + e.getMessage());
            }
            stmt.close();

            // Call stored procedure with IN parameters
            String procedureCall = "{call add_employee(?, ?, ?, ?)}";
            cstmt = conn.prepareCall(procedureCall);

            // Set IN parameters
            // Employee ID
            cstmt.setInt(1, 1001);
            // Name
            cstmt.setString(2, "Zhang San");
            // Department
            cstmt.setString(3, "IT");
            // Salary
            cstmt.setDouble(4, 8000.00);

            // Execute stored procedure
            cstmt.execute();

            // Check if update count available
            int updateCount = cstmt.getUpdateCount();
            System.out.println("Affected rows: " + updateCount);

        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            // Close resources (Order: CallableStatement -> Connection)
            if (cstmt != null) {
                try {
                    cstmt.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
            if (conn != null) {
                try {
                    conn.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```

### Stored Procedure with OUT Parameters

Stored procedures can return values through OUT parameters.

```java
package quickstart;

import java.sql.*;
import java.util.Properties;
import java.math.BigDecimal;

public class OUTParameterExample {
    public static void main(String[] args) {
        // Database connection parameters
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        // Declare resource objects
        Connection conn = null;
        CallableStatement cstmt = null;

        // Load driver
        try {
            Class.forName("com.yashandb.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            return;
        }

        try {
            // Get database connection
            conn = DriverManager.getConnection(url, info);

            // First create table and stored procedure (if not exists)
            Statement stmt = conn.createStatement();
            try {
                stmt.execute("CREATE TABLE IF NOT EXISTS employee(id INTEGER, name VARCHAR(100), department VARCHAR(50), salary DOUBLE)");
                // Insert test data
                stmt.execute("DELETE FROM employee WHERE id = 1001");
                stmt.execute("INSERT INTO employee (id, name, department, salary) VALUES (1001, 'Zhang San', 'IT', 8000)");
            } catch (SQLException e) {
                // Table may already exist
            }

            try {
                stmt.execute("CREATE OR REPLACE PROCEDURE get_employee_salary(" +
                    "p_id IN INTEGER, p_salary OUT DOUBLE) AS " +
                    "BEGIN " +
                    "  SELECT salary INTO p_salary FROM employee WHERE id = p_id; " +
                    "END get_employee_salary;");
            } catch (SQLException e) {
                e.printStackTrace();
            }
            stmt.close();

            // Call stored procedure with OUT parameters
            String procedureCall = "{call get_employee_salary(?, ?)}";
            cstmt = conn.prepareCall(procedureCall);

            // Set IN parameter
            // Employee ID
            cstmt.setInt(1, 1001);

            // Register OUT parameter
            cstmt.registerOutParameter(2, Types.DECIMAL);

            // Execute stored procedure
            cstmt.execute();

            // Get OUT parameter value
            BigDecimal salary = cstmt.getBigDecimal(2);
            System.out.println("Employee 1001 salary: " + salary);

        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            // Close resources (Order: CallableStatement -> Connection)
            if (cstmt != null) {
                try {
                    cstmt.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
            if (conn != null) {
                try {
                    conn.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```

### Stored Procedure with INOUT Parameters

INOUT parameters can serve as both input and receive output values.

```java
package quickstart;

import java.sql.*;
import java.util.Properties;
import java.math.BigDecimal;

public class INOUTParameterExample {
    public static void main(String[] args) {
        // Database connection parameters
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        // Declare resource objects
        Connection conn = null;
        CallableStatement cstmt = null;

        // Load driver
        try {
            Class.forName("com.yashandb.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            return;
        }

        try {
            // Get database connection
            conn = DriverManager.getConnection(url, info);

            // First create table and stored procedure (if not exists)
            Statement stmt = conn.createStatement();
            try {
                stmt.execute("CREATE TABLE IF NOT EXISTS employee(id INTEGER, name VARCHAR(100), department VARCHAR(50), salary DOUBLE)");
                // Insert test data
                stmt.execute("DELETE FROM employee WHERE id = 1001");
                stmt.execute("INSERT INTO employee (id, name, department, salary) VALUES (1001, 'Zhang San', 'IT', 8000)");
            } catch (SQLException e) {
                // Table may already exist
            }

            try {
                stmt.execute("CREATE OR REPLACE PROCEDURE update_salary(" +
                    "p_id IN INTEGER, p_salary IN OUT DOUBLE) AS " +
                    "BEGIN " +
                    "  UPDATE employee SET salary = p_salary WHERE id = p_id; " +
                    "  SELECT salary INTO p_salary FROM employee WHERE id = p_id; " +
                    "END update_salary;");
            } catch (SQLException e) {
                e.printStackTrace();
            }
            stmt.close();

            // Call stored procedure with INOUT parameters
            String procedureCall = "{call update_salary(?, ?)}";
            cstmt = conn.prepareCall(procedureCall);

            // Set IN parameter value
            // Employee ID
            cstmt.setInt(1, 1001);
            // New salary
            cstmt.setDouble(2, 9000.00);

            // Register INOUT parameter
            cstmt.registerOutParameter(2, Types.DECIMAL);

            // Execute stored procedure
            cstmt.execute();

            // Get output value of INOUT parameter
            BigDecimal actualSalary = cstmt.getBigDecimal(2);
            System.out.println("Updated salary: " + actualSalary);

        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            // Close resources (Order: CallableStatement -> Connection)
            if (cstmt != null) {
                try {
                    cstmt.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
            if (conn != null) {
                try {
                    conn.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```

## Stored Procedure Returning ResultSet

Stored procedures can return result sets through OUT parameters, which can be obtained through getResultSet() method.

```java
package quickstart;

import java.sql.*;
import java.util.Properties;
import java.math.BigDecimal;

public class ResultSetProcedureExample {
    public static void main(String[] args) {
        // Database connection parameters
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        // Declare resource objects
        Connection conn = null;
        Statement stmt = null;
        ResultSet rs = null;

        // Load driver
        try {
            Class.forName("com.yashandb.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            return;
        }

        try {
            // Get database connection
            conn = DriverManager.getConnection(url, info);

            // First create table (if not exists)
            stmt = conn.createStatement();
            try {
                stmt.execute("CREATE TABLE IF NOT EXISTS employee(id INTEGER, name VARCHAR(100), department VARCHAR(50), salary DOUBLE)");
                // Insert test data
                stmt.execute("DELETE FROM employee WHERE department = 'IT'");
                stmt.execute("INSERT INTO employee (id, name, department, salary) VALUES (1, 'Zhang San', 'IT', 8000)");
                stmt.execute("INSERT INTO employee (id, name, department, salary) VALUES (2, 'Li Si', 'IT', 9000)");
            } catch (SQLException e) {
                // Table may already exist
            }

            // Execute query directly to get result set
            String querySql = "SELECT id, name, department, salary FROM employee WHERE department = 'IT'";
            rs = stmt.executeQuery(querySql);

            System.out.println("IT Department Employees:");
            while (rs.next()) {
                int empId = rs.getInt("ID");
                String name = rs.getString("NAME");
                String department = rs.getString("DEPARTMENT");
                double salary = rs.getDouble("SALARY");

                System.out.printf("ID: %d, Name: %s, Department: %s, Salary: %.2f%n",
                            empId, name, department, salary);
            }

        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            // Close resources (Order: ResultSet -> Statement -> Connection)
            if (rs != null) {
                try {
                    rs.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
            if (stmt != null) {
                try {
                    stmt.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
            if (conn != null) {
                try {
                    conn.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```

JDBC使用CallableStatement接口来调用数据库存储过程和函数。

## 调用存储过程基础

### 无参数存储过程

最简单的存储过程调用是不带参数的情况。

```java
package quickstart;

import java.sql.*;
import java.util.Properties;

public class SimpleProcedureCallExample {
    public static void main(String[] args) {
        // 数据库连接参数
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        // 声明资源对象
        Connection conn = null;
        CallableStatement cstmt = null;
        ResultSet rs = null;

        // 加载驱动
        try {
            Class.forName("com.yashandb.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            return;
        }

        try {
            // 获取数据库连接
            conn = DriverManager.getConnection(url, info);

            // 首先创建存储过程（如果不存在）
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

            // 调用无参数存储过程
            String procedureCall = "{call get_system_time()}";
            cstmt = conn.prepareCall(procedureCall);

            // 执行存储过程
            cstmt.execute();

            // 获取结果
            rs = cstmt.getResultSet();
            if (rs != null) {
                while (rs.next()) {
                    // 获取系统时间
                    Timestamp systemTime = rs.getTimestamp(1);
                    System.out.println("System time: " + systemTime);
                }
            }

        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            // 关闭资源（顺序：ResultSet -> CallableStatement -> Connection）
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

### 带IN参数的存储过程

存储过程可以接受输入参数，通过set方法设置参数值。

```java
package quickstart;

import java.sql.*;
import java.util.Properties;

public class INParameterExample {
    public static void main(String[] args) {
        // 数据库连接参数
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        // 声明资源对象
        Connection conn = null;
        CallableStatement cstmt = null;

        // 加载驱动
        try {
            Class.forName("com.yashandb.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            return;
        }

        try {
            // 获取数据库连接
            conn = DriverManager.getConnection(url, info);

            // 首先创建表和存储过程（如果不存在）
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

            // 调用带IN参数的存储过程
            String procedureCall = "{call add_employee(?, ?, ?, ?)}";
            cstmt = conn.prepareCall(procedureCall);

            // 设置IN参数
            // 员工ID
            cstmt.setInt(1, 1001);
            // 姓名
            cstmt.setString(2, "Zhang San");
            // 部门
            cstmt.setString(3, "IT");
            // 薪资
            cstmt.setDouble(4, 8000.00);

            // 执行存储过程
            cstmt.execute();

            // 检查是否有更新计数
            int updateCount = cstmt.getUpdateCount();
            System.out.println("Affected rows: " + updateCount);

        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            // 关闭资源（顺序：CallableStatement -> Connection）
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

### 带OUT参数的存储过程

存储过程可以通过OUT参数返回值。

```java
package quickstart;

import java.sql.*;
import java.util.Properties;
import java.math.BigDecimal;

public class OUTParameterExample {
    public static void main(String[] args) {
        // 数据库连接参数
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        // 声明资源对象
        Connection conn = null;
        CallableStatement cstmt = null;

        // 加载驱动
        try {
            Class.forName("com.yashandb.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            return;
        }

        try {
            // 获取数据库连接
            conn = DriverManager.getConnection(url, info);

            // 首先创建表和存储过程（如果不存在）
            Statement stmt = conn.createStatement();
            try {
                stmt.execute("CREATE TABLE IF NOT EXISTS employee(id INTEGER, name VARCHAR(100), department VARCHAR(50), salary DOUBLE)");
                // 插入测试数据
                stmt.execute("DELETE FROM employee WHERE id = 1001");
                stmt.execute("INSERT INTO employee (id, name, department, salary) VALUES (1001, 'Zhang San', 'IT', 8000)");
            } catch (SQLException e) {
                // 表可能已存在
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

            // 调用带OUT参数的存储过程
            String procedureCall = "{call get_employee_salary(?, ?)}";
            cstmt = conn.prepareCall(procedureCall);

            // 设置IN参数
            // 员工ID
            cstmt.setInt(1, 1001);

            // 注册OUT参数
            cstmt.registerOutParameter(2, Types.DECIMAL);

            // 执行存储过程
            cstmt.execute();

            // 获取OUT参数值
            BigDecimal salary = cstmt.getBigDecimal(2);
            System.out.println("Employee 1001 salary: " + salary);

        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            // 关闭资源（顺序：CallableStatement -> Connection）
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

### 带INOUT参数的存储过程

INOUT参数既可以作为输入，也可以接收输出值。

```java
package quickstart;

import java.sql.*;
import java.util.Properties;
import java.math.BigDecimal;

public class INOUTParameterExample {
    public static void main(String[] args) {
        // 数据库连接参数
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        // 声明资源对象
        Connection conn = null;
        CallableStatement cstmt = null;

        // 加载驱动
        try {
            Class.forName("com.yashandb.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            return;
        }

        try {
            // 获取数据库连接
            conn = DriverManager.getConnection(url, info);

            // 首先创建表和存储过程（如果不存在）
            Statement stmt = conn.createStatement();
            try {
                stmt.execute("CREATE TABLE IF NOT EXISTS employee(id INTEGER, name VARCHAR(100), department VARCHAR(50), salary DOUBLE)");
                // 插入测试数据
                stmt.execute("DELETE FROM employee WHERE id = 1001");
                stmt.execute("INSERT INTO employee (id, name, department, salary) VALUES (1001, 'Zhang San', 'IT', 8000)");
            } catch (SQLException e) {
                // 表可能已存在
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

            // 调用带INOUT参数的存储过程
            String procedureCall = "{call update_salary(?, ?)}";
            cstmt = conn.prepareCall(procedureCall);

            // 设置IN参数值
            // 员工ID
            cstmt.setInt(1, 1001);
            // 新薪资
            cstmt.setDouble(2, 9000.00);

            // 注册INOUT参数
            cstmt.registerOutParameter(2, Types.DECIMAL);

            // 执行存储过程
            cstmt.execute();

            // 获取INOUT参数的输出值
            BigDecimal actualSalary = cstmt.getBigDecimal(2);
            System.out.println("Updated salary: " + actualSalary);

        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            // 关闭资源（顺序：CallableStatement -> Connection）
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

## 返回结果集的存储过程

存储过程可以通过OUT参数返回结果集，然后通过getResultSet()方法获取。

```java
package quickstart;

import java.sql.*;
import java.util.Properties;
import java.math.BigDecimal;

public class ResultSetProcedureExample {
    public static void main(String[] args) {
        // 数据库连接参数
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");

        // 声明资源对象
        Connection conn = null;
        Statement stmt = null;
        ResultSet rs = null;

        // 加载驱动
        try {
            Class.forName("com.yashandb.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            return;
        }

        try {
            // 获取数据库连接
            conn = DriverManager.getConnection(url, info);

            // 首先创建表（如果不存在）
            stmt = conn.createStatement();
            try {
                stmt.execute("CREATE TABLE IF NOT EXISTS employee(id INTEGER, name VARCHAR(100), department VARCHAR(50), salary DOUBLE)");
                // 插入测试数据
                stmt.execute("DELETE FROM employee WHERE department = 'IT'");
                stmt.execute("INSERT INTO employee (id, name, department, salary) VALUES (1, 'Zhang San', 'IT', 8000)");
                stmt.execute("INSERT INTO employee (id, name, department, salary) VALUES (2, 'Li Si', 'IT', 9000)");
            } catch (SQLException e) {
                // 表可能已存在
            }

            // 直接执行查询获取结果集
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
            // 关闭资源（顺序：ResultSet -> Statement -> Connection）
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

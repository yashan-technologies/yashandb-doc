The ResultSet object represents the result set returned by a database query. It is a scrollable, updatable result set that can be used to traverse the query results.

## ResultSet Usage

### Creating ResultSet

ResultSet create demo.

```java
package quickstart;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.Statement;
import java.sql.SQLException;

public class ResultSetDemo {
    // Database connection parameters, replace with your own configuration.
    private static final String URL = "jdbc:yasdb://192.168.1.2:1688/yasdb";
    private static final String USER = "sales";
    private static final String PASSWORD = "sales";

    public static void main(String[] args) {
        // Declare resource objects: Connection/Statement/ResultSet
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
            // Get the database connection
            conn = DriverManager.getConnection(URL, USER, PASSWORD);

            // Create a Statement object via the Connection.createStatement() method
            stmt = conn.createStatement();

            // Example: Execute insert operation
            String insertSql = "INSERT INTO customer(id, name) VALUES (2, 'Tom')";
            // Execute insert operation and return the number of affected rows
            int affectedRows = stmt.executeUpdate(insertSql);
            System.out.println("Data inserted successfully, number of affected rows: " + affectedRows);

            // Example: Execute query
            String querySql = "SELECT id, name FROM customer WHERE id = 2";

            // Execute query operation and return the result set
            rs = stmt.executeQuery(querySql);

            // Process the result set
            while (rs.next()) {
                int id = rs.getInt("id");
                String name = rs.getString("name");
                System.out.println("Query Result: ID=" + id + ", Name=" + name);
            }

        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            // Close resources (Order: ResultSet -> Statement -> Connection)
            try {
                if (rs != null) rs.close();
                if (stmt != null) stmt.close();
                if (conn != null) conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```

##  Basic ResultSet Operations

###  Traversing ResultSet

ResultSet provides the next() method to move the cursor to the next row of data.

```java
package quickstart;

import java.sql.*;
import java.util.Properties;

public class ResultSetBasicsExample {
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
            // Create Statement object
            stmt = conn.createStatement();
            // Execute query
            rs = stmt.executeQuery("SELECT ID, NAME FROM CUSTOMER");

            // Traverse the result set
            while (rs.next()) {
                // Get data by column index
                int id = rs.getInt(1);
                // Get data by column name
                String name = rs.getString("NAME");

                System.out.println("ID: " + id + ", Name: " + name);
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

###  Retrieving Data

ResultSet provides various get methods to retrieve different types of data.

```java
package quickstart;

import java.sql.*;
import java.util.Properties;

public class ResultSetDataTypesExample {
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
            // Create Statement object
            stmt = conn.createStatement();
            // Execute query
            rs = stmt.executeQuery("SELECT ID, NAME FROM CUSTOMER");

            while (rs.next()) {
                // Retrieve different types of data
                // Integer type
                int id = rs.getInt("ID");
                // String type
                String name = rs.getString("NAME");

                System.out.println("ID: " + id + ", Name: " + name);
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

##  Advanced ResultSet Features

###  Scrollable ResultSet

By specifying ResultSet types, advanced features such as forward/backward scrolling and absolute positioning can be implemented.

```java
package quickstart;

import java.sql.*;
import java.util.Properties;

public class ScrollableResultSetExample {
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
            // Create scrollable but insensitive result set
            stmt = conn.createStatement(
                ResultSet.TYPE_SCROLL_INSENSITIVE,
                ResultSet.CONCUR_READ_ONLY);

            // Execute query
            rs = stmt.executeQuery("SELECT ID, NAME FROM CUSTOMER");

            // Move to last row
            rs.last();
            System.out.println("Total rows: " + rs.getRow());

            // Move to first row
            rs.first();
            System.out.println("First employee: " + rs.getString("NAME"));

            // Move to specific row
            rs.absolute(5);
            System.out.println("5th employee: " + rs.getString("NAME"));

            // Relative movement
            rs.relative(-2);
            System.out.println("3rd employee: " + rs.getString("NAME"));

            // Move to previous row
            while (rs.previous()) {
                System.out.println("Employee: " + rs.getString("NAME"));
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

###  Updatable ResultSet

By specifying concurrency type, updatable result sets can be created to directly modify data in the database.

```java
package quickstart;

import java.sql.*;
import java.util.Properties;

public class UpdatableResultSetExample {
    public static void main(String[] args) {
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

        Connection conn = null;
        Statement stmt = null;
        ResultSet rs = null;

        try {
            conn = DriverManager.getConnection(url, info);
            conn.setAutoCommit(false);

            // Create updatable result set
            stmt = conn.createStatement(
                ResultSet.TYPE_SCROLL_INSENSITIVE,
                ResultSet.CONCUR_UPDATABLE);

            rs = stmt.executeQuery("SELECT ID, NAME FROM CUSTOMER");

            // Traverse result set and update data
            while (rs.next()) {
                String name = rs.getString("NAME");

              
                String newName = name;
                newName = name + "_upd";
                rs.updateString("NAME", newName);
                // Update database
                rs.updateRow();
                System.out.println("Updated name: " + name);
            }

            conn.commit();

        } catch (SQLException e) {
            e.printStackTrace();
            if (conn != null) {
                try {
                    conn.rollback();
                } catch (SQLException ex) {
                    ex.printStackTrace();
                }
            }
        } finally {
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

##  ResultSet Metadata

ResultSetMetaData provides structural information about the result set, including column count, column names, column types, etc.

```java
package quickstart;

import java.sql.*;
import java.util.Properties;

public class ResultSetMetaDataExample {
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
            // Create Statement object
            stmt = conn.createStatement();
            // Execute query
            rs = stmt.executeQuery("SELECT ID, NAME FROM CUSTOMER");

            // Get result set metadata
            ResultSetMetaData metaData = rs.getMetaData();

            // Get column count
            int columnCount = metaData.getColumnCount();
            System.out.println("Total columns: " + columnCount);
            System.out.println("----------------------------------------");

            // Traverse metadata for each column
            for (int i = 1; i <= columnCount; i++) {
                // Column name
                String columnName = metaData.getColumnName(i);
                // Column label
                String columnLabel = metaData.getColumnLabel(i);
                // Column type
                int columnType = metaData.getColumnType(i);
                // Type name
                String typeName = metaData.getColumnTypeName(i);
                // Column size
                int columnSize = metaData.getColumnDisplaySize(i);
                // Nullable
                int nullable = metaData.isNullable(i);

                System.out.println("Column " + i + ":");
                System.out.println("  Name: " + columnName);
                System.out.println("  Label: " + columnLabel);
                System.out.println("  Type: " + columnType + " (" + typeName + ")");
                System.out.println("  Size: " + columnSize);
                System.out.println("  Nullable: " + (nullable == ResultSetMetaData.columnNullable ? "Yes" : "No"));
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

##  ResultSet Performance Optimization

Ensure ResultSet is properly closed, avoiding resource leaks.

```java
package quickstart;

import java.sql.*;
import java.util.Properties;

public class ResourceManagementExample {
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
            // Create Statement object
            stmt = conn.createStatement();
            // Execute query
            rs = stmt.executeQuery("SELECT ID, NAME FROM CUSTOMER");

            // Process result set
            while (rs.next()) {
                System.out.println("Customer: " + rs.getString("NAME"));
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


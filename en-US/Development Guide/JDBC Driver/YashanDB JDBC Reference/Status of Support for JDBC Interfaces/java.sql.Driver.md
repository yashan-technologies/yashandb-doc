## Description

`java.sql.Driver` represents the core interface for database drivers defined in the JDBC specification. This interface is located in the `java.sql` package, and its definition is included with the JDK by default. Its core functions are as follows:


- Used to identify connectable URLs.

- Defines the interaction specification between Java programs and specific database drivers.
- Responsible for creating `Connection` (database connection) objects, serving as the "entry point" for programs to connect to databases;
- Provides basic information about the driver (such as version number, and whether a specific URL is accepted).

Detailed interface specifications, please refer to [The JDBC Official Website](https://docs.oracle.com/en/java/javase/17/docs/api/java.sql/java/sql/Driver.html).

## Method

The YashanDB JDBC driver has the following methods for the Driver interface:

|Method |Return Type |
| --- | --- |
| connect(String url, java.util.Properties info) | Connection |
| acceptsURL(String url)  | boolean |
| getPropertyInfo(String url,java.util.Properties info)  | DriverPropertyInfo\[\] |
| getMajorVersion() | int |
| getMinorVersion() | int |
| jdbcCompliant() | boolean |

## Example

```java
package quickstart;

import java.sql.Connection;
import java.sql.Driver;
import java.sql.DriverManager;
import java.sql.DriverPropertyInfo;
import java.sql.SQLException;
import java.util.Properties;

public class CustomDriverManager {

    public static Connection getConnection(String url, String user, String password)
            throws SQLException {
        // 1. Find suitable driver
        Driver suitableDriver = findSuitableDriver(url);
        if (suitableDriver == null) {
            throw new SQLException("No suitable driver found for URL: " + url);
        }

        // 2. Check driver version
        int majorVersion = suitableDriver.getMajorVersion();
        int minorVersion = suitableDriver.getMinorVersion();
        System.out.println("Using driver version: " + majorVersion + "." + minorVersion);

        // 3. Check JDBC compliance
        if (!suitableDriver.jdbcCompliant()) {
            System.out.println("Warning: Driver is not fully JDBC compliant");
        }

        // 4. Get connection property requirements
        Properties baseProps = new Properties();
        baseProps.setProperty("user", user);
        baseProps.setProperty("password", password);

        DriverPropertyInfo[] requiredProps = suitableDriver.getPropertyInfo(url, baseProps);
        for (DriverPropertyInfo prop : requiredProps) {
            if (prop.required && prop.value == null) {
                System.out.println("Missing required property: " + prop.name);
            }
        }

        // 5. Establish connection
        Properties connectionProps = new Properties();
        connectionProps.setProperty("user", user);
        connectionProps.setProperty("password", password);

        return suitableDriver.connect(url, connectionProps);
    }

    private static Driver findSuitableDriver(String url) throws SQLException {
        // Traverse registered drivers to find the matching one
        java.util.Enumeration<Driver> drivers = DriverManager.getDrivers();
        while (drivers.hasMoreElements()) {
            Driver driver = drivers.nextElement();
            if (driver.acceptsURL(url)) {
                return driver;
            }
        }
        return null;
    }
}
```


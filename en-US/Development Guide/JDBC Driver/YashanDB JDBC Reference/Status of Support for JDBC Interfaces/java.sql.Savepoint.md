## Description

`java.sql.Savepoint` represents that it is used to represent a savepoint object. It can be either named or unnamed. Instances are created via the `Connection.setSavepoint()` method and are only valid while the transaction is uncommitted or not rolled back. Its core functions are as follows:


- Creates a savepoint within an active database transaction, splitting a large transaction into multiple segments that can be rolled back independently.
- Supports rolling back to a specified savepoint (only undoing operations after the savepoint, while preserving operations before it).
- Allows manually releasing unused savepoints to conserve database resources.

Detailed interface specifications, please refer to [The JDBC Official Website](https://docs.oracle.com/en/java/javase/17/docs/api/java.sql/java/sql/Savepoint.html).

## Method

The YashanDB JDBC driver has the following methods for the Savepoint interface:

|Method |Return Type |Notes |
|--------------------|----------------------|-----------------------|
| getSavepointId()   | int                  | Only unnamed savepoints can call this; otherwise, an exception will be thrown. |
| getSavepointName() | String            | Only named savepoints can call this; otherwise, an exception will be thrown. |

## Example

```java
package quickstart;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.sql.Savepoint;
import java.util.Properties;

public class SavepointExample {

    public static Connection getConnection() throws SQLException {
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");
        Class.forName("com.yashandb.jdbc.Driver");
        return DriverManager.getConnection(url, info);
    }

    // Conditional rollback to savepoint
    public void conditionalRollback(Connection conn) throws SQLException {
        conn.setAutoCommit(false);

        Savepoint checkpoint1 = conn.setSavepoint("CHECKPOINT_1");
        System.out.println("Checkpoint 1: " + checkpoint1.getSavepointName());

        // Execute some operations
        boolean success = performOperation(conn);

        if (!success) {
            // Rollback to savepoint
            conn.rollback(checkpoint1);
            System.out.println("Operation failed, rolled back to: " +
                    checkpoint1.getSavepointName());
        } else {
            Savepoint checkpoint2 = conn.setSavepoint("CHECKPOINT_2");
            System.out.println("Checkpoint 2 ID: " + checkpoint2.getSavepointId());

            // Continue execution...
        }

        conn.commit();
        conn.setAutoCommit(true);
    }

    private boolean performOperation(Connection conn) throws SQLException {
        // Perform operation
        return true;
    }
}
```


DML operations are automatically committed by default at runtime, which is known as auto-commit mode.

You can also disable auto-commit mode by calling the following method on the Connection object:

   ```java
   conn.setAutoCommit(false);
   ```
If auto-commit mode is disabled, you must call the corresponding methods on the Connection object to manually commit or roll back changes, as shown below:

   ```java
   conn.commit();
   ```

Or use the following command:

   ```java
   conn.rollback();
   ```

A COMMIT or ROLLBACK operation affects all subsequent DML statements executed thereafter.
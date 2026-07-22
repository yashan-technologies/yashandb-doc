The Python driver supports transaction commit, rollback, and isolation level operations.

## Manual Commit Mode

By default, the YashanDB Python driver is in manual commit mode (autocommit=False). In this mode, each SQL statement requires manual `commit()` after execution, or `rollback()` when errors occur.

```python
# Check commit mode
print(connection.autocommit)  # Output: False
```

### Applicable Scenarios

Manual commit is suitable for scenarios where multiple SQL statements need to be executed as a single atomic transaction:

- Batch Data Operations: When inserting, updating, or deleting multiple related data records, manual commit ensures atomicity of these operations.

- Multi-table Operations: When a business logic involves modifications to multiple tables, manual commit ensures data consistency.

- Rollback Capability: If errors occur during execution, you need the ability to rollback already executed operations.

```python
# Default manual commit mode
cursor.execute("INSERT INTO table1 VALUES (1, 'a')")
cursor.execute("INSERT INTO table2 VALUES (2, 'b')")

# Commit transaction
connection.commit()
```

## Auto-commit

To enable auto-commit mode, set the `autocommit` attribute to `True`. In auto-commit mode, each SQL statement is automatically committed after execution.

```python
# Enable auto-commit
connection.autocommit = True

# Each SQL statement executed will be auto-committed
cursor.execute("INSERT INTO table1 VALUES (1, 'a')")  # Auto-committed
```

### Applicable Scenarios

Auto-commit is suitable for the following scenarios:

- Simple Single SQL Operations: Each execution of a single SQL statement that does not require transaction protection.

- Rapid Prototype Development: Want to quickly verify SQL execution results during development and debugging.

- Read-only Operations: Read-only queries do not require transaction protection.

## Rollback

```python
try:
    cursor.execute("INSERT INTO example_table VALUES (1, 'test')")
    connection.commit()
except Exception as e:
    # Rollback on error
    connection.rollback()
    print(f"Error: {e}")
```

### Applicable Scenarios

Rollback is used to undo all changes made in the current transaction, typically used in the following scenarios:

- Execution Errors: When SQL statement execution encounters errors, rollback can undo partially executed operations.

- Business Validation Failure: When business logic validation fails, rollback is needed to undo executed operations.

- User Cancellation: When users actively cancel the current operation, rollback ensures data is not contaminated.

### Rollback to Savepoint

For complex transactions, you can create savepoints to support partial rollback:

```python
# Execute operations and create savepoint (no need to commit first, just create within the transaction)
cursor.execute("INSERT INTO table1 VALUES (1, 'a')")
cursor.execute("SAVEPOINT sp1")  # Create savepoint
cursor.execute("INSERT INTO table2 VALUES (2, 'b')")

# Rollback to savepoint (undo table2 insert, keep table1 insert)
cursor.execute("ROLLBACK TO SAVEPOINT sp1")

# Commit transaction (only table1 insert is committed)
connection.commit()
```

> **Note**：
>
> - **Do NOT** call `commit()` before creating a savepoint, because `commit()` commits the current transaction, ending it and invalidating the savepoint.
> - `ROLLBACK TO SAVEPOINT` undoes all operations after the savepoint but keeps operations before the savepoint.
> - Savepoint names cannot be reused within the same transaction. After a rollback, the savepoint is deleted.

## Transaction Isolation Level

Transaction isolation level defines the degree to which a transaction may be affected by other concurrent transactions. YashanDB only partially supports the following isolation levels:

- READ COMMITTED (Default): Can only read committed data, avoiding dirty reads, but may produce non-repeatable reads. This level is **always available**.

- SERIALIZABLE: Highest isolation level, fully serialized execution, avoiding phantom reads, but may affect concurrent performance. **Only supported as the first statement in a transaction**.

> **Note**：
>
> - YashanDB **does not support** READ UNCOMMITTED and REPEATABLE READ isolation levels. Attempting to set these will return a syntax error.
> - The SERIALIZABLE level must be executed as the first statement of a transaction, otherwise error `YAS-02029` will be returned.

### Setting Transaction Isolation Level

> **Note**：
>
> The YashanDB Python driver **does not support** the `connection.isolation_level` attribute. To set transaction isolation level, please use SQL statements.

```python
# Set transaction isolation level (via SQL statement)
cursor.execute("SET TRANSACTION ISOLATION LEVEL READ COMMITTED")
# Or set immediately after connection
conn.cursor().execute("ALTER SESSION SET ISOLATION_LEVEL = READ COMMITTED")
```

### Isolation Level Selection Guidelines

- READ COMMITTED: Suitable for most application scenarios, a balanced choice between performance and consistency.

- SERIALIZABLE: Suitable for scenarios requiring extremely high data consistency, but ensure it is executed as the first statement of a transaction.

## Complete Example

```python
import yaspy

try:
    # Connect to database
    conn = yaspy.connect('sales/sales@192.168.1.2:1688')
    cursor = conn.cursor()

    # Create test table
    cursor.execute('DROP TABLE IF EXISTS test_transaction')
    cursor.execute('CREATE TABLE test_transaction (id INT, name VARCHAR(50), amount DECIMAL(10,2))')
    conn.commit()
    print('Table created')

    # 1. Manual commit mode test (default mode)
    cursor.execute("INSERT INTO test_transaction VALUES (1, 'Product A', 100.00)")
    cursor.execute("INSERT INTO test_transaction VALUES (2, 'Product B', 200.00)")
    conn.commit()
    print('Manual commit: Records 1 and 2 committed')

    # Verify manually committed data
    cursor.execute('SELECT * FROM test_transaction')
    rows = cursor.fetchall()
    print(f'After manual commit: {len(rows)} records')

    # 2. Auto-commit mode test
    conn.autocommit = True
    cursor.execute("INSERT INTO test_transaction VALUES (3, 'Product C', 300.00)")
    print('Auto-commit: Record 3 inserted')

    cursor.execute("INSERT INTO test_transaction VALUES (4, 'Product D', 400.00)")
    print('Auto-commit: Record 4 inserted')

    # Verify auto-committed data
    cursor.execute('SELECT * FROM test_transaction')
    rows = cursor.fetchall()
    print(f'After auto-commit: {len(rows)} records')

    # 3. Restore manual commit and test rollback
    conn.autocommit = False
    cursor.execute("INSERT INTO test_transaction VALUES (5, 'Product E', 500.00)")
    print('Record 5 inserted (will be rolled back)')

    # Execute rollback
    conn.rollback()
    print('Rollback executed')

    # Verify data after rollback
    cursor.execute('SELECT * FROM test_transaction')
    rows = cursor.fetchall()
    print(f'After rollback: {len(rows)} records')

    # 4. Set transaction isolation level (via SQL statement)
    cursor.execute("SET TRANSACTION ISOLATION LEVEL READ COMMITTED")
    print('Isolation level set to: READ COMMITTED')

    # Clean up test table
    cursor.execute('DROP TABLE test_transaction')
    conn.commit()
    print('Test table cleaned up')

    cursor.close()
    conn.close()
    print('Connection closed')

except Exception as e:
    print(f'Error: {e}')
```

Execute:

```python
Table created
Manual commit: Records 1 and 2 committed
After manual commit: 2 records
Auto-commit: Record 3 inserted
Auto-commit: Record 4 inserted
After auto-commit: 4 records
Record 5 inserted (will be rolled back)
Rollback executed
After rollback: 4 records
Isolation level set to: READ COMMITTED
Test table cleaned up
Connection closed
```

Where:
- Table created: Test table created successfully
- Manual commit: Records 1 and 2 committed: Records 1 and 2 committed manually
- After manual commit: 2 records: 2 records after manual commit
- Auto-commit: Record 3 inserted: Record 3 inserted in auto-commit mode
- Auto-commit: Record 4 inserted: Record 4 inserted in auto-commit mode
- After auto-commit: 4 records: 4 records after auto-commit
- Record 5 inserted (will be rolled back): Record 5 inserted (will be rolled back)
- Rollback executed: Rollback executed successfully
- After rollback: 4 records: 4 records after rollback (5th record rolled back)
- Isolation level set to: READ COMMITTED: Isolation level set to READ COMMITTED
- Test table cleaned up: Test table cleaned up
- Connection closed: Connection closed

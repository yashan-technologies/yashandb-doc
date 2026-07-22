The connection object is used to represent and manage a single connection between a Python application and a database.

## Object Creation Method

The connection object must be created using [connect\(\)](./python-yaspy Module) or [poolname.acquire\(\)](./Connection Pool Object).

## Connection Attributes

|Attribute |Description |
| ---------- | ------ |
| Connection.autocommit | Transaction auto-commit mode, defaults to False (manual commit). |
| Connection.username (Read-only) | Database username. |
| Connection.dsn (Read-only) | Data source name. |

## Connection Methods

|Method |Description |
| ---------- | ------ |
| Connection.close()    | Close the connection now. |
| Connection.commit()   | Commit any pending transaction. When autocommit=True, calling commit() will throw an exception. |
| Connection.rollback() | Roll back any pending transaction.  |
| Connection.cursor()   | Create a new [Cursor Object](./Cursor Object). |

> **Note**：
>
> - The `connection.isolation_level` attribute does not exist. If you need to set transaction isolation level, please use SQL statement `SET TRANSACTION ISOLATION LEVEL ...`.
> - When `autocommit=True`, calling `commit()` or `rollback()` will both throw `NotSupportedError`.

## Complete Example

```python
import yaspy

# Connect to database
conn = yaspy.connect(dsn="sales/sales@192.168.1.2:1688")

# View connection attributes
print(f'Autocommit: {conn.autocommit}')
print(f'Username: {conn.username}')
print(f'DSN: {conn.dsn}')

# 1. cursor() - Create cursor
cursor = conn.cursor()

# Create test table
cursor.execute("DROP TABLE IF EXISTS test_connection")
cursor.execute("""
    CREATE TABLE test_connection (
        id INT PRIMARY KEY,
        name VARCHAR(50),
        amount DECIMAL(10, 2)
    )
""")
conn.commit()
print('Table created')

# 2. commit() - Commit transaction
cursor.execute("INSERT INTO test_connection VALUES (1, 'Product A', 100.00)")
cursor.execute("INSERT INTO test_connection VALUES (2, 'Product B', 200.00)")
conn.commit()
print('Data committed via commit()')

# 3. Test rollback()
# Insert data without committing
cursor.execute("INSERT INTO test_connection VALUES (3, 'Product C', 300.00)")
print('Data inserted (not committed yet)')

# 4. rollback() - Rollback uncommitted transaction
conn.rollback()
print('Transaction rolled back')

# Verify data - should only have 2 records
cursor.execute("SELECT * FROM test_connection")
rows = cursor.fetchall()
print(f'After rollback: {len(rows)} records')

# Re-insert data and commit
cursor.execute("INSERT INTO test_connection VALUES (3, 'Product C', 300.00)")
conn.commit()
print('Data committed')

# Verify all data
cursor.execute("SELECT * FROM test_connection ORDER BY id")
rows = cursor.fetchall()
print(f'After commit: {len(rows)} records:')
for row in rows:
    print(f'  id={row[0]}, name={row[1]}, amount={row[2]}')

# 5. close() - Close cursor
cursor.close()
print('Cursor closed')

# Clean up test table
conn.cursor().execute("DROP TABLE test_connection")
conn.commit()
print('Table cleaned up')

# 6. close() - Close connection
conn.close()
print('Connection closed')
```

Execute:

```text
Autocommit: False
Username: sales
DSN: 192.168.1.2:1688
Table created
Data committed via commit()
Data inserted (not committed yet)
Transaction rolled back
After rollback: 2 records
Data committed
After commit: 3 records:
  id=1, name=Product A, amount=100.0
  id=2, name=Product B, amount=200.0
  id=3, name=Product C, amount=300.0
Cursor closed
Table cleaned up
Connection closed
```

Where:
- Autocommit: False: Default manual commit mode
- Username: sales: Database username
- DSN: 192.168.1.2:1688: Data source name (host address and port only, without user info)
- Table created: Test table created successfully
- Data committed via commit(): Data committed via commit()
- Data inserted (not committed yet): Data inserted but not committed
- Transaction rolled back: Transaction rolled back
- After rollback: 2 records: Only 2 records after rollback
- Data committed: Data committed
- After commit: 3 records: 3 records after commit
- Table cleaned up: Test table cleaned up
- Cursor closed: Cursor closed
- Connection closed: Connection closed

Properly closing database connections is a good programming practice.

> **Note**
>
> - Closing the connection pool will terminate all connections in the pool. Ensure all ongoing connection operations have been completed before closing.
> - When using connection pools, the correct closing order is: first call `pool.release(connection=conn)` to return the connection, then call `pool.close()` to close the connection pool. **Do NOT call `pool.close()` directly without first returning the connection**, otherwise the connection may not be released correctly.

## Complete Example

```python
import yaspy

# Create connection (standalone connection mode)
connection = yaspy.connect(dsn="sales/sales@192.168.1.2:1688")
print("Connection created")

# Create cursor
cursor = connection.cursor()
print("Cursor created")

# Create test table and insert data
cursor.execute("CREATE TABLE IF NOT EXISTS example_table (id INT, name VARCHAR(50))")
cursor.execute("INSERT INTO example_table VALUES (1, 'test')")
connection.commit()

# Execute query operation
cursor.execute("SELECT * FROM example_table")
result = cursor.fetchall()
print(f"Query result: {result}")

# ========== Close Cursor ==========
cursor.close()
print("Cursor closed")

# ========== Close Connection ==========
connection.close()
print("Connection closed")

# ========== Close Connection Pool (only for connection pool mode) ==========
# If using connection pool (SessionPool), also need to close the connection pool
# pool.close()
# print("Connection pool closed")
```

Execute:

```python
$ python close_example.py
Connection created
Cursor created
Query result: [(1, 'test')]
Cursor closed
Connection closed
```

Where:
- Connection created: Connection created successfully
- Cursor created: Cursor created successfully
- Query result: [(1, 'test')]: Query execution result
- Cursor closed: Cursor closed successfully
- Connection closed: Connection closed successfully

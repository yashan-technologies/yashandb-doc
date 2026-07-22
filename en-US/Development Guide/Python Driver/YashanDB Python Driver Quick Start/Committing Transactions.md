Committing a transaction saves pending changes to the database. By default, the YashanDB Python driver is in manual commit mode (autocommit=False), and each SQL statement requires manually calling commit() to submit.

```python
# Check auto-commit status
print(connection.autocommit)

# Enable auto-commit
connection.autocommit = True

# Disable auto-commit (restore manual commit mode)
connection.autocommit = False
```

## Complete Example

```python
import yaspy

try:
    # Connect to database
    conn = yaspy.connect('sales/sales@192.168.1.2:1688')

    # Check default autocommit status
    print(f'Default autocommit: {conn.autocommit}')
    # Default autocommit: False

    cursor = conn.cursor()

    # Create test table
    cursor.execute('DROP TABLE IF EXISTS test_commit')
    cursor.execute('CREATE TABLE test_commit (id INT, name VARCHAR(50))')
    conn.commit()
    print('Table created')

    # Insert data in manual commit mode
    cursor.execute("INSERT INTO test_commit VALUES (1, 'Alice')")
    cursor.execute("INSERT INTO test_commit VALUES (2, 'Bob')")
    conn.commit()
    print('Records committed')

    # Query and verify data
    cursor.execute('SELECT * FROM test_commit ORDER BY id')
    rows = cursor.fetchall()
    print(f'Total records: {len(rows)}')
    for row in rows:
        print(f'  ID: {row[0]}, Name: {row[1]}')

    # Clean up test table
    cursor.execute('DROP TABLE test_commit')
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
Default autocommit: False
Table created
Records committed
Total records: 2
  ID: 1, Name: Alice
  ID: 2, Name: Bob
Test table cleaned up
Connection closed
```

Where:
- Default autocommit: False: Default autocommit is False (manual commit mode)
- Table created: Table created successfully
- Records committed: Records committed successfully
- Total records: 2: Total number of records returned
- Test table cleaned up: Test table cleaned up
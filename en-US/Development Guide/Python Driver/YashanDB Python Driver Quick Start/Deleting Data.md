Use the execute() method to execute DELETE statements to delete data.

## Complete Example

```python
import yaspy

# Connect to database
conn = yaspy.connect(dsn='sales/sales@192.168.1.2:1688')
cursor = conn.cursor()

# Create test table
cursor.execute("DROP TABLE IF EXISTS example_table")
cursor.execute("CREATE TABLE example_table (id INT PRIMARY KEY, name VARCHAR(50))")
cursor.execute("INSERT INTO example_table VALUES (1, 'test1')")
cursor.execute("INSERT INTO example_table VALUES (2, 'test2')")
cursor.execute("INSERT INTO example_table VALUES (3, 'test3')")
conn.commit()

# View data before delete
cursor.execute("SELECT * FROM example_table")
print(f"Before delete: {cursor.fetchall()}")

# Delete data
cursor.execute("DELETE FROM example_table WHERE id = 1")

# Commit transaction
conn.commit()

# Get affected rows
print(f"Affected rows: {cursor.rowcount}")

# View data after delete
cursor.execute("SELECT * FROM example_table")
print(f"After delete: {cursor.fetchall()}")

cursor.close()
conn.close()
print("Test completed")
```

Execute:

```text
$ python delete_example.py
Before delete: [(1, 'test1'), (2, 'test2'), (3, 'test3')]
Affected rows: 1
After delete: [(2, 'test2'), (3, 'test3')]
Test completed
```

Where:
- Before delete: Data before delete
- Affected rows: Number of affected rows (how many records were deleted)
- After delete: Data after delete

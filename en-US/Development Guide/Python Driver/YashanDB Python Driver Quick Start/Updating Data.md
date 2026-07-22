Use the execute() method to execute UPDATE statements to update data.

## Complete Example

```python
cursor = connection.cursor()

# Query data before update
cursor.execute("SELECT * FROM example_table WHERE id = 1")
print(f"Before update: {cursor.fetchone()}")

# Update data
cursor.execute("UPDATE example_table SET name = 'updated' WHERE id = 1")

# Commit transaction
connection.commit()

# Get affected rows
print(f"Affected rows: {cursor.rowcount}")

# Query data after update
cursor.execute("SELECT * FROM example_table WHERE id = 1")
print(f"After update: {cursor.fetchone()}")

cursor.close()
```

Execute:

```python
$ python update_example.py
Before update: (1, 'original')
Affected rows: 1
After update: (1, 'updated')
```

Where:
- Before update: Data before update
- Affected rows: Number of affected rows (how many records were updated)
- After update: Data after update

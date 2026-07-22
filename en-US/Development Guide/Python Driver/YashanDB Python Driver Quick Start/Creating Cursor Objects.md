Cursor objects are used to execute SQL statements and retrieve results.

## Cursor Attributes

|Attribute |Description |
| ------------------ | -------------------- |
| description | Returns column information of the result set|
| rowcount |Returns the number of affected rows |
| arraysize |Gets/sets the number of rows returned by each fetch |

## Cursor Methods

|Method |Description |
| --------------- | -------------------- |
| execute() | Execute single SQL statement|
| executemany() |Batch execute SQL statements |
| fetchone() |Fetch single record |
| fetchall() |Fetch all records|
| fetchmany() |Fetch multiple records |
| close() |Close cursor |

## Complete Example

```python
# Create cursor through connection object
cursor = connection.cursor()
print("Cursor created successfully")

# Execute SQL statement (assuming example_table exists)
cursor.execute("SELECT * FROM example_table")
print("Query executed successfully")

# Fetch results
result = cursor.fetchone()
print(f"Result: {result}")

# View column information
print(f"Description: {cursor.description}")

# Get rowcount (usually 0 or -1 for SELECT statements immediately after execute; may change to fetched rows count after fetch)
print(f"Rowcount: {cursor.rowcount}")

# Close cursor
cursor.close()
print("Cursor closed")
```

Execute:

```python
$ python cursor_example.py
Cursor created successfully
Query executed successfully
Result: ('C001', 5)
Description: [FetchInfo(name='CUSTOMER_ID', type=26, ...), FetchInfo(name='TOTAL_ORDERS', type=4, ...)]
Rowcount: 1
Cursor closed
```

Where:
- Cursor created successfully: Cursor created successfully
- Query executed successfully: Query executed successfully
- Result: Query result returned
- Description: Column information of the result set (each element in `cursor.description` is a FetchInfo object)
- Rowcount: Number of affected rows (may be inaccurate for SELECT statements)
- Cursor closed: Cursor closed successfully
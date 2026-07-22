Use the cursor object to execute SQL statements and retrieve query results. The cursor object is the core object for executing SQL statements and retrieving results.

## Execute Query

```python
# Create cursor
cursor = connection.cursor()

# Execute query
cursor.execute("SELECT * FROM example_table WHERE id = :id", {"id": 1})
```

## Query Single Record

Use the fetchone() method to fetch the next record from the result set.

```python
cursor = connection.cursor()

# Execute query
cursor.execute("SELECT * FROM example_table WHERE id = 1")

# Fetch single record
row = cursor.fetchone()
print(row)

cursor.close()
```

## Query Multiple Records

Use the fetchall() method to fetch all records from the result set.

```python
cursor = connection.cursor()

# Execute query
cursor.execute("SELECT * FROM example_table")

# Fetch all records
rows = cursor.fetchall()
for row in rows:
    print(row)

cursor.close()
```

## Fetch Multiple Records

Use the fetchmany() method to fetch the specified number of records.

```python
cursor = connection.cursor()

# Execute query
cursor.execute("SELECT * FROM example_table")

# Fetch 5 records each time
while True:
    rows = cursor.fetchmany(5)
    if not rows:
        break
    for row in rows:
        print(row)

cursor.close()
```

## Iterate Using Cursor

Cursor objects are iterable and can be used directly in loops.

```python
cursor = connection.cursor()

# Execute query
cursor.execute("SELECT * FROM example_table")

# Iterate using cursor
for row in cursor:
    print(row)

cursor.close()
```

## Loop Using fetchone

```python
cursor = connection.cursor()

# Execute query
cursor.execute("SELECT * FROM example_table")

# Loop using fetchone
row = cursor.fetchone()
while row:
    print(row)
    row = cursor.fetchone()

cursor.close()
```

## Get Column Information

Use the description property to get column information from the result set (column name, type, etc.).

```python
cursor = connection.cursor()

# Execute query
cursor.execute("SELECT id, name, created_at FROM example_table")

# Get column information
for desc in cursor.description:
    print(desc)
# Output example: FetchInfo(name='id', type_code=<class 'int'>, ...)

cursor.close()
```

> **Note**：
>
> Each element in `cursor.description` is a `FetchInfo` object, containing attributes like `name` (column name) and `type_code` (column type), not a simple tuple. You can access the column name via `desc.name`.

## Complete Example

```python
import yaspy

try:
    # Connect to database
    conn = yaspy.connect(dsn="sales/sales@192.168.1.2:1688")

    # Create cursor
    cursor = conn.cursor()

    # Execute query
    cursor.execute("SELECT id, name, created_at FROM example_table WHERE status = :status", {"status": 1})

    # Check row count
    print(f"Total rows: {cursor.rowcount}")

    # View column information
    print("Column info:")
    for desc in cursor.description:
        print(f"  {desc[0]}: {desc[1]}")

    # Fetch all data
    rows = cursor.fetchall()

    # Print results
    for row in rows:
        print(f"ID: {row[0]}, Name: {row[1]}, Created: {row[2]}")

    # Close cursor
    cursor.close()

    # Close connection
    conn.close()

except Exception as e:
    print(f"Error: {e}")
```

Execute:

```python
$ python query_example.py
Total rows: 0
Column info:
  CUSTOMER_ID: FetchInfo(name='CUSTOMER_ID', type_code=12, ...)
  TOTAL_ORDERS: FetchInfo(name='TOTAL_ORDERS', type_code=4, ...)
ID: C001, Total Orders: 5
```

Where:
- Total rows: Number of rows returned. **For SELECT statements, rowcount is usually 0 or -1**, which is the standard DB-API behavior. The actual row count should be obtained via `len()` on the result from `fetchall()` and similar methods.
- Column info: Column information, each element is a `FetchInfo` object with `name` attribute for column name and `type_code` for column type code.
- ID: C001, Total Orders: 5: Actual data returned from the query.


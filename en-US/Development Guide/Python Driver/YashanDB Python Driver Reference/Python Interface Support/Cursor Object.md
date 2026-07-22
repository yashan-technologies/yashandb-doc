The cursor object is used to manage operations on specific content in the database, such as executing SQL statements and retrieving execution results.

## Object Creation Method

A cursor object is created through the [connection\.cursor\(\)](./Connection Object) method.

## Cursor Methods

|Method |Description |
| ------------- | -------- |
| Cursor.close() | Immediately closes the cursor. |
| Cursor.execute() | Executes an SQL operation, such as a query. |
| Cursor.executemany() | Executes batch SQL operations. |
| Cursor.fetchone() | Fetches a single row of data from the query result set, returning the row data or None, where None indicates there are no more rows to return. |
| Cursor.fetchmany() | Retrieves a portion of the query result set according to the specified size (arraysize attribute), returning a list containing the specified number of rows, with each row as a tuple (or dictionary). |
| Cursor.fetchall() | Retrieves all rows of the query result set at once. |
| Cursor.var() | Creates bind variables for interacting with the database. |
| Cursor.callproc()    | Calls a stored procedure. Internally constructs and executes `begin proc(...); end;` statement. |

> **Note**:
>
> The following cursor methods are not currently implemented and will throw `NotSupportedError` when called:
> - `nextset()`
> - `setinputsizes()`
> - `setoutputsize()`

## Cursor Attributes

|Attribute |Description |
| ----------- |------ |
| Cursor.description: list[FetchInfo] &#124; None | Metadata about query result columns, including column names and types. <br/>Read-only, automatically generated after executing an SQL statement. |
| Cursor.rowcount: int | Number of rows generated or affected by the last execute(). <br/>Read-only, automatically updated after SQL execution. |
| Cursor.arraysize: int | Specifies the number of rows to fetch with fetchmany(). <br/>Writable, e.g., `cursor.arraysize = 100`. |

## Context Management Protocol

The cursor object supports Python's context manager protocol and can use the `with` statement to automatically manage the cursor lifecycle.

```python
# Use with statement to manage cursor
with conn.cursor() as cursor:
    cursor.execute("SELECT * FROM table")
    for row in cursor:
        print(row)
# Cursor automatically closed
```

## Iteration Protocol

The cursor object supports the iteration protocol and can directly iterate through query results, equivalent to calling `fetchone()` in a loop until None is returned (triggering `StopIteration` when the result set is exhausted).

```python
# Iterate cursor directly to get all rows
cursor.execute("SELECT * FROM table")
for row in cursor:
    print(row)
# Equivalent to:
# cursor.execute("SELECT * FROM table")
# while True:
#     row = cursor.fetchone()
#     if row is None:
#         break
#     print(row)
```

## Complete Example

```python
import yaspy

# Connect to database
conn = yaspy.connect(dsn="sales/sales@192.168.1.2:1688")

# Create cursor
cursor = conn.cursor()

# Create test table
cursor.execute("DROP TABLE IF EXISTS test_cursor")
cursor.execute("""
    CREATE TABLE test_cursor (
        id INT PRIMARY KEY,
        name VARCHAR(50),
        price DECIMAL(10, 2)
    )
""")
conn.commit()
print('Table created')

# 1. execute() - Execute single SQL
cursor.execute("INSERT INTO test_cursor VALUES (1, 'Apple', 5.99)")
cursor.execute("INSERT INTO test_cursor VALUES (2, 'Banana', 3.50)")
cursor.execute("INSERT INTO test_cursor VALUES (3, 'Orange', 4.25)")
conn.commit()
print('Data inserted via execute()')

# Check rowcount - number of rows affected by last execute
print(f'Rows affected: {cursor.rowcount}')

# 2. executemany() - Batch execute SQL
# Method 1: Using keyword binding (list of dicts)
data = [
    {"id": 4, "name": "Mango", "price": 6.99},
    {"id": 5, "name": "Grape", "price": 7.50},
    {"id": 6, "name": "Peach", "price": 5.25}
]
cursor.executemany("INSERT INTO test_cursor VALUES (:id, :name, :price)", data)
conn.commit()
print('Data inserted via executemany() (named binding)')

# Method 2: Using positional binding (list of tuples), id does not overlap with Method 1
data_tuple = [
    (7, 'Pear', 4.99),
    (8, 'Cherry', 8.50),
    (9, 'Plum', 3.25)
]
cursor.executemany("INSERT INTO test_cursor VALUES (:1, :2, :3)", data_tuple)
conn.commit()
print('Data inserted via executemany() (positional binding)')

# 3. fetchone() - Fetch single row
cursor.execute("SELECT * FROM test_cursor WHERE id = 1")
row = cursor.fetchone()
print(f'fetchone() - Row: id={row[0]}, name={row[1]}, price={row[2]}')

# 4. fetchmany() - Fetch multiple rows
cursor.execute("SELECT * FROM test_cursor ORDER BY id")
rows = cursor.fetchmany(2)
print(f'fetchmany(2) - Got {len(rows)} rows:')
for row in rows:
    print(f'  id={row[0]}, name={row[1]}, price={row[2]}')

# Set arraysize attribute
cursor.arraysize = 2
cursor.execute("SELECT * FROM test_cursor ORDER BY id")
rows = cursor.fetchmany()
print(f'fetchmany() with arraysize=2 - Got {len(rows)} rows:')
for row in rows:
    print(f'  id={row[0]}, name={row[1]}, price={row[2]}')

# 5. fetchall() - Fetch all data
cursor.execute("SELECT * FROM test_cursor ORDER BY id")
all_rows = cursor.fetchall()
print(f'fetchall() - Got {len(all_rows)} rows:')
for row in all_rows:
    print(f'  id={row[0]}, name={row[1]}, price={row[2]}')

# 6. description - Get query result column info
cursor.execute("SELECT id, name, price FROM test_cursor WHERE id = 1")
desc = cursor.description
print('Description:')
for col in desc:
    print(f'  Column: {col[0]}, Type: {col[1]}')

# 7. var() - Create bind variable
id_var = cursor.var(yaspy.INTEGER)
name_var = cursor.var(yaspy.VARCHAR)
price_var = cursor.var(yaspy.NUMBER)
id_var.setvalue(7)
name_var.setvalue('Pear')
price_var.setvalue(4.99)
cursor.execute("INSERT INTO test_cursor VALUES (:id, :name, :price)",
               {"id": id_var, "name": name_var, "price": price_var})
conn.commit()
print('Data inserted via var()')

# Verify data inserted via var()
cursor.execute("SELECT * FROM test_cursor WHERE id = 7")
row = cursor.fetchone()
print(f'Verify var() - Row: id={row[0]}, name={row[1]}, price={row[2]}')

# 8. close() - Close cursor
cursor.close()
print('Cursor closed')

# Clean up test table
conn.cursor().execute("DROP TABLE test_cursor")
conn.commit()
print('Table cleaned up')

# Close connection
conn.close()
print('Connection closed')
```

Execute:

```text
Table created
Data inserted via execute()
Rows affected: 1
Data inserted via executemany() (named binding)
Data inserted via executemany() (positional binding)
fetchone() - Row: id=1, name=Apple, price=5.99
fetchmany(2) - Got 2 rows:
  id=1, name=Apple, price=5.99
  id=2, name=Banana, price=3.5
fetchmany() with arraysize=2 - Got 2 rows:
  id=3, name=Orange, price=4.25
  id=4, name=Mango, price=6.99
fetchall() - Got 9 rows:
  id=1, name=Apple, price=5.99
  id=2, name=Banana, price=3.5
  id=3, name=Orange, price=4.25
  id=4, name=Mango, price=6.99
  id=5, name=Grape, price=7.5
  id=6, name=Peach, price=5.25
  id=7, name=Pear, price=4.99
  id=8, name=Cherry, price=8.5
  id=9, name=Plum, price=3.25
Description:
  Column: ID, Type: 4
  Column: NAME, Type: 26
  Column: PRICE, Type: 12
Data inserted via var()
Verify var() - Row: id=7, name=Pear, price=4.99
Cursor closed
Table cleaned up
Connection closed
```

Where:
- Table created: Test table created successfully
- Data inserted via execute(): Data inserted successfully via execute()
- Rows affected: 1: Number of rows affected by last SQL
- Data inserted via executemany() (named binding): Batch data inserted successfully via named binding
- Data inserted via executemany() (positional binding): Batch data inserted successfully via positional binding
- fetchone() - Row: id=1, name=Apple, price=5.99: Fetch single row
- fetchmany(2) - Got 2 rows: Fetch first 2 rows (starting from the beginning of result set)
- fetchmany() with arraysize=2: Using arraysize attribute to fetch data (continues from the last position)
- fetchall() - Got 9 rows: Fetch all 9 rows
- Description: Column info of query result. `col[0]` is column name, `col[1]` is YashanDB type code (4=INTEGER, 26=VARCHAR, 12=FLOAT), not Python built-in type
- Data inserted via var(): Data inserted successfully via var() bind variable
- Verify var(): Verify data inserted via var()
- Cursor closed: Cursor closed
- Table cleaned up: Test table cleaned up
- Connection closed: Connection closed

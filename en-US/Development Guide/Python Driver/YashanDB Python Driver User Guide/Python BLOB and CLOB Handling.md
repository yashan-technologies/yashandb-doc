The Python driver supports handling BLOB and CLOB large object data.

## Large Object Introduction

- **BLOB**: Used to store binary data such as images, audio, video, files and other unstructured data.

- **CLOB**: Used to store large amounts of text data such as long articles, XML documents, JSON data and other character data.

## Complete Example

```python
import yaspy

# Connect to database
conn = yaspy.connect('sales/sales@192.168.1.2:1688')
cursor = conn.cursor()

# Create table with BLOB and CLOB columns
cursor.execute('''CREATE TABLE IF NOT EXISTS test_lob (
    id NUMBER,
    blob_col BLOB,
    clob_col CLOB
)''')
conn.commit()
print('Table created successfully')

# Insert BLOB data (using positional binding)
data = b'\x00\x01\x02\x03\x04\x05'
cursor.execute("INSERT INTO test_lob (id, blob_col) VALUES (:1, :2)", (1, data))
conn.commit()
print('BLOB inserted successfully')

# Read BLOB data (use separate cursor to avoid conflict with var() operations)
read_cursor = conn.cursor()
read_cursor.execute("SELECT blob_col FROM test_lob WHERE id = :1", (1,))
row = read_cursor.fetchone()
if row and row[0]:
    print(f'BLOB read successfully, size: {len(row[0])}')
read_cursor.close()

# Insert CLOB data (using positional binding)
text = "This is a large text content " * 10
cursor.execute("INSERT INTO test_lob (id, clob_col) VALUES (:1, :2)", (2, text))
conn.commit()
print('CLOB inserted successfully')

# Read CLOB data (use separate cursor)
read_cursor = conn.cursor()
read_cursor.execute("SELECT clob_col FROM test_lob WHERE id = :1", (2,))
row = read_cursor.fetchone()
if row and row[0]:
    print(f'CLOB read successfully, length: {len(row[0])}')
read_cursor.close()

# Use var() to bind BLOB/CLOB (use separate cursor)
var_cursor = conn.cursor()

# Insert BLOB data (using var)
blob_var = var_cursor.var(yaspy.BLOB)
blob_var.setvalue(b'\x00\x01\x02\x03\x04\x05')
var_cursor.execute("INSERT INTO test_lob (id, blob_col) VALUES (:1, :2)", (3, blob_var))
conn.commit()
print('BLOB inserted via var() successfully')

# Insert CLOB data (using var)
clob_var = var_cursor.var(yaspy.CLOB)
clob_var.setvalue("This is a large text content " * 10)
var_cursor.execute("INSERT INTO test_lob (id, clob_col) VALUES (:1, :2)", (4, clob_var))
conn.commit()
print('CLOB inserted via var() successfully')

var_cursor.close()

# Close cursor and connection
cursor.close()
conn.close()
```

> **Note**：
>
> - When reading LOB data, it is recommended to use a separate cursor to avoid conflicts with `var().setvalue()` operations on the same cursor, which may cause `SystemError`.
> - When using `var()` to insert LOB data, it is recommended to operate on a separate cursor.

Execute:

```python
Table created successfully
BLOB inserted successfully
BLOB read successfully, size: 6
CLOB inserted successfully
CLOB read successfully, length: 290
BLOB inserted via var() successfully
CLOB inserted via var() successfully
```

Where:
- Table created successfully: Test table created successfully
- BLOB inserted successfully: BLOB data inserted successfully
- BLOB read successfully, size: 6: BLOB data read successfully, size is 6 bytes
- CLOB inserted successfully: CLOB data inserted successfully
- CLOB read successfully, length: 290: CLOB data read successfully, length is 290 characters
- BLOB inserted via var() successfully: BLOB data inserted via var() successfully
- CLOB inserted via var() successfully: CLOB data inserted via var() successfully

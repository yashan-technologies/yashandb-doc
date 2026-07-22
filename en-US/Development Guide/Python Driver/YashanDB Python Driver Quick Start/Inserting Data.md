Use the execute() method to execute INSERT statements to insert data.

## Insert Single Record

```python
cursor = connection.cursor()

# Insert single record
cursor.execute("INSERT INTO example_table VALUES (1, 'test1')")

# Commit transaction
connection.commit()

cursor.close()
```

## Batch Insert

```python
cursor = connection.cursor()

# Batch insert data
data = [
    (2, 'test2'),
    (3, 'test3'),
    (4, 'test4')
]
cursor.executemany("INSERT INTO example_table VALUES (?, ?)", data)

# Commit transaction
connection.commit()

cursor.close()
```

## Insert Data with Parameters

```python
cursor = connection.cursor()

# Insert with parameters
cursor.execute("INSERT INTO example_table VALUES (?, ?)", (5, 'test5'))

# Commit transaction
connection.commit()

cursor.close()
```

## Complete Example

```python
import yaspy

try:
    # Connect to database
    conn = yaspy.connect('sales/sales@192.168.1.2:1688')
    cursor = conn.cursor()

    # Create test table
    cursor.execute('DROP TABLE IF EXISTS products')
    cursor.execute('''
        CREATE TABLE products (
            id INT PRIMARY KEY,
            name VARCHAR(100),
            price DECIMAL(10, 2),
            stock INT
        )
    ''')
    conn.commit()
    print('Table created')

    # Insert single record
    cursor.execute("INSERT INTO products VALUES (1, 'Laptop', 5999.00, 50)")
    conn.commit()
    print('Single record inserted')

    # Batch insert data (using named binding)
    products = [
        {'id': 2, 'name': 'Mouse', 'price': 99.00, 'stock': 200},
        {'id': 3, 'name': 'Keyboard', 'price': 299.00, 'stock': 150},
        {'id': 4, 'name': 'Monitor', 'price': 1999.00, 'stock': 80}
    ]
    cursor.executemany(
        "INSERT INTO products VALUES (:id, :name, :price, :stock)",
        products
    )
    conn.commit()
    print('Batch records inserted')

    # Insert with parameters (using a new cursor to avoid binding conflict)
    cursor2 = conn.cursor()
    cursor2.execute(
        "INSERT INTO products VALUES (:id, :name, :price, :stock)",
        {'id': 5, 'name': 'Headphones', 'price': 599.00, 'stock': 100}
    )
    conn.commit()
    cursor2.close()
    print('Parameterized record inserted')

    # Query and verify all data
    cursor.execute('SELECT * FROM products ORDER BY id')
    rows = cursor.fetchall()
    print(f'Total products: {len(rows)}')
    for row in rows:
        print(f'  ID: {row[0]}, Name: {row[1]}, Price: {row[2]}, Stock: {row[3]}')

    # Clean up test table
    cursor.execute('DROP TABLE products')
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
$ python insert_complete_example.py
Table created
Single record inserted
Batch records inserted
Parameterized record inserted
Total products: 5
  ID: 1, Name: Laptop, Price: 5999.0, Stock: 50
  ID: 2, Name: Mouse, Price: 99.0, Stock: 200
  ID: 3, Name: Keyboard, Price: 299.0, Stock: 150
  ID: 4, Name: Monitor, Price: 1999.0, Stock: 80
  ID: 5, Name: Headphones, Price: 599.0, Stock: 100
Test table cleaned up
Connection closed
```

Where:
- Table created: Test table created successfully
- Single record inserted: Single record inserted successfully
- Batch records inserted: Batch insert successful (multiple records at once)
- Parameterized record inserted: Parameterized insert successful
- Total products: 5: Total number of products returned
- Test table cleaned up: Test table cleaned up

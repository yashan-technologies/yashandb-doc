Use the cursor object's execute() method to execute DDL statements to create tables.

## Create Table

```python
# Create cursor object
cursor = connection.cursor()

# Execute create table statement
cursor.execute("DROP TABLE IF EXISTS example_table")
cursor.execute("CREATE TABLE example_table (id INT PRIMARY KEY, name VARCHAR(50))")

# Commit transaction
connection.commit()

# Close cursor
cursor.close()
```

## Create Table with Constraints

```python
cursor = connection.cursor()

# Create table with constraints
cursor.execute("""
    CREATE TABLE example_table (
        id INT PRIMARY KEY,
        name VARCHAR(50) NOT NULL,
        email VARCHAR(100) UNIQUE,
        age INT CHECK (age >= 0),
        created_at TIMESTAMP DEFAULT SYSDATE
    )
""")

connection.commit()
cursor.close()
```

## Create Index

```python
cursor = connection.cursor()

# Create regular index
cursor.execute("CREATE INDEX idx_name ON example_table (name)")

# Create composite index
cursor.execute("CREATE INDEX idx_name_age ON example_table (name, age)")

connection.commit()
cursor.close()
```

> **Note**:
>
> For columns with `UNIQUE` constraint defined in the CREATE TABLE statement (e.g., `email VARCHAR(100) UNIQUE`), the database automatically creates a unique index. Do not create a duplicate unique index on the same column.

## Complete Example

```python
import yaspy

try:
    # Connect to database
    conn = yaspy.connect('sales/sales@192.168.1.2:1688')
    cursor = conn.cursor()

    # Drop table if exists
    cursor.execute('DROP TABLE IF EXISTS employee')
    conn.commit()

    # Create table with constraints
    cursor.execute('''
        CREATE TABLE employee (
            emp_id INT PRIMARY KEY,
            emp_name VARCHAR(50) NOT NULL,
            email VARCHAR(100) UNIQUE,
            salary DECIMAL(10,2) CHECK (salary >= 0),
            dept_id INT,
            hire_date DATE DEFAULT SYSDATE
        )
    ''')
    conn.commit()
    print('Table created successfully')

    # Create indexes
    cursor.execute('CREATE INDEX idx_emp_name ON employee(emp_name)')
    cursor.execute('CREATE INDEX idx_dept ON employee(dept_id)')
    # Note: The UNIQUE constraint on email column already creates a unique index automatically
    conn.commit()
    print('Indexes created successfully')

    # Insert test data
    cursor.execute("INSERT INTO employee VALUES (1, 'Zhang San', 'zhangsan@example.com', 8000.00, 101, SYSDATE)")
    cursor.execute("INSERT INTO employee VALUES (2, 'Li Si', 'lisi@example.com', 9000.00, 101, SYSDATE)")
    cursor.execute("INSERT INTO employee VALUES (3, 'Wang Wu', 'wangwu@example.com', 7500.00, 102, SYSDATE)")
    conn.commit()
    print('Data inserted successfully')

    # Query and verify
    cursor.execute('SELECT * FROM employee')
    rows = cursor.fetchall()
    print(f'Total employees: {len(rows)}')
    for row in rows:
        print(f'  ID: {row[0]}, Name: {row[1]}, Email: {row[2]}, Salary: {row[3]}, Dept: {row[4]}')

    # Clean up test data
    cursor.execute('DROP TABLE employee')
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
$ python create_table_complete_example.py
Table created successfully
Indexes created successfully
Data inserted successfully
Total employees: 3
  ID: 1, Name: Zhang San, Email: zhangsan@example.com, Salary: 8000, Dept: 101
  ID: 2, Name: Li Si, Email: lisi@example.com, Salary: 9000, Dept: 101
  ID: 3, Name: Wang Wu, Email: wangwu@example.com, Salary: 7500, Dept: 102
Test table cleaned up
Connection closed
```

Where:
- Table created successfully: Table created successfully
- Indexes created successfully: Indexes created successfully
- Data inserted successfully: Test data inserted successfully
- Total employees: 3: Number of employees returned
- Test table cleaned up: Test table cleaned up

The Python driver supports calling stored procedures.

## Call Stored Procedure Without Parameters

```python
# Call stored procedure without parameters
cursor.execute("CALL procedure_name()")
connection.commit()
```

## Call Stored Procedure With Parameters

```python
# Call stored procedure with input parameters (using positional binding :1, :2)
cursor.execute("CALL procedure_with_params(:1, :2)", (1, 'test'))
connection.commit()
```

> **Note**：
>
> The YashanDB Python driver **only supports** `:1`, `:2` and other positional binding parameters, or `:param` named binding parameters, **does not support** `?` placeholders.

## Call Stored Procedure With Output Parameters

The YashanDB Python driver uses the `cursor.var()` method to create bind variables to handle OUT parameters.

```python
# Call stored procedure with output parameters

# Create bind variables to receive OUT parameters
out_param = cursor.var(str)

# Call stored procedure, OUT parameter is passed via bind variable
cursor.execute("CALL procedure_with_out(:param)", {"param": out_param})

# Get the value of OUT parameter
print(out_param.getvalue())
```

> **Note**：
>
> `cursor.var(str)` is mapped by the driver to `VARCHAR` (default length 8000) and functions correctly. If you need to explicitly specify the type and length, you can also write `cursor.var(yaspy.VARCHAR, 100)`, which is consistent with the style in the [Cursor Object](../YashanDB Python Driver Reference/Python Interface Support/Cursor Object.md) section.

## Call Function

```python
# Call function (using positional binding :1, :2)
cursor.execute("SELECT function_name(:1, :2)", (1, 'test'))
result = cursor.fetchone()
print(result)
```

## Complete Example

```python
import yaspy

# Connect to database
conn = yaspy.connect('sales/sales@192.168.1.2:1688')
cursor = conn.cursor()

# Create test table
cursor.execute('''CREATE TABLE IF NOT EXISTS test_proc_table (
    id NUMBER,
    message VARCHAR2(100)
)''')
conn.commit()
print('Table created successfully')

# Create stored procedure without parameters
cursor.execute('''
    CREATE OR REPLACE PROCEDURE test_proc AS
    BEGIN
        INSERT INTO test_proc_table VALUES (1, 'Hello from procedure');
    END;
''')
conn.commit()
print('Procedure created successfully')

# Call stored procedure without parameters
cursor.execute('CALL test_proc()')
conn.commit()
print('Procedure called successfully')

# Query to verify result
cursor.execute('SELECT * FROM test_proc_table')
row = cursor.fetchone()
print(f'Result: {row}')
# Result: (1, 'Hello from procedure')

# Create stored procedure with parameters
cursor.execute('''
    CREATE OR REPLACE PROCEDURE test_proc_with_params(p_id NUMBER, p_msg VARCHAR2) AS
    BEGIN
        INSERT INTO test_proc_table VALUES (p_id, p_msg);
    END;
''')
conn.commit()
print('Procedure with params created successfully')

# Call stored procedure with parameters (using positional binding :1, :2)
cursor.execute('CALL test_proc_with_params(:1, :2)', (2, 'Test message'))
conn.commit()
print('Procedure with params called successfully')

# Query to verify result
cursor.execute('SELECT * FROM test_proc_table ORDER BY id')
rows = cursor.fetchall()
for row in rows:
    print(f'Result: {row}')
# Result: (1, 'Hello from procedure')
# Result: (2, 'Test message')

# Create function
cursor.execute('''
    CREATE OR REPLACE FUNCTION test_func(p_id NUMBER) RETURN VARCHAR2 AS
        v_msg VARCHAR2(100);
    BEGIN
        SELECT message INTO v_msg FROM test_proc_table WHERE id = p_id;
        RETURN v_msg;
    END;
''')
conn.commit()
print('Function created successfully')

# Call function
cursor.execute('SELECT test_func(1) FROM DUAL')
result = cursor.fetchone()
print(f'Function result: {result}')
# Function result: ('Hello from procedure',)

# Create stored procedure with OUT parameter
cursor.execute('''
    CREATE OR REPLACE PROCEDURE test_proc_with_out(
        p_id NUMBER,
        p_msg OUT VARCHAR2
    ) AS
    BEGIN
        SELECT message INTO p_msg FROM test_proc_table WHERE id = p_id;
    END;
''')
conn.commit()
print('Procedure with OUT parameter created successfully')

# Call stored procedure with OUT parameter
out_msg = cursor.var(str)
cursor.execute(
    "CALL test_proc_with_out(:id, :msg)",
    {"id": 1, "msg": out_msg}
)
conn.commit()
print(f'OUT parameter result: {out_msg.getvalue()}')
# OUT parameter result: Hello from procedure

# Close cursor and connection
cursor.close()
conn.close()
```

Execute:

```python
Table created successfully
Procedure created successfully
Procedure called successfully
Result: (1, 'Hello from procedure')
Procedure with params created successfully
Procedure with params called successfully
Result: (1, 'Hello from procedure')
Result: (2, 'Test message')
Function created successfully
Function result: ('Hello from procedure',)
Procedure with OUT parameter created successfully
OUT parameter result: Hello from procedure
```

Where:
- Table created successfully: Test table created successfully
- Procedure created successfully: Stored procedure without parameters created successfully
- Procedure called successfully: Stored procedure without parameters called successfully
- Result: (1, 'Hello from procedure'): Query result is record 1, message is 'Hello from procedure'
- Procedure with params created successfully: Stored procedure with parameters created successfully
- Procedure with params called successfully: Stored procedure with parameters called successfully
- Function created successfully: Function created successfully
- Function result: ('Hello from procedure',): Function returned result
- Procedure with OUT parameter created successfully: Stored procedure with OUT parameter created successfully
- OUT parameter result: Hello from procedure: OUT parameter returned result

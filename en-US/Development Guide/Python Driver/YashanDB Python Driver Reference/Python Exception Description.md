The Python driver may throw various exceptions during execution.

## Exception Class Hierarchy

The yaspy driver follows the [DB-API 2.0 specification](https://www.python.org/dev/peps/pep-0249/) and defines the following exception class hierarchy. However, note that **in the current version of yaspy 1.2.1, all database operation errors are thrown as `DatabaseError` and will not be subdivided into `IntegrityError`, `ProgrammingError`, `OperationalError`, etc.** These subclass exception types cannot be directly caught for the time being.

```python
Exception (Built-in Python)
├── yaspy.Error
│   ├── InterfaceError
│   └── DatabaseError           # ← The only database error type thrown by yaspy in the current version
│       ├── DataError
│       ├── OperationalError
│       ├── IntegrityError
│       ├── InternalError
│       ├── ProgrammingError
│       └── NotSupportedError
└── yaspy.Warning
```

## Exception Class Description

| Exception Class | Description |
| -------------------------- | -------------------- |
| yaspy.Error | Base class for database error classes; `InterfaceError`, `DatabaseError` and their subclasses all inherit from this class |
| yaspy.Warning | Warning exception, thrown when non-fatal issues occur (such as data truncation) |
| yaspy.InterfaceError | Connection parameter error, such as DSN format error, missing required parameters. **Invalid host address or port connection failures usually throw `DatabaseError` rather than `InterfaceError`** |
| yaspy.DatabaseError | Base class for database execution errors. **In the current version of yaspy, all database operation errors (primary key conflicts, SQL syntax errors, table not found, etc.) are thrown as this type or its subclasses**, and will not be further subdivided into the subclasses listed in the table below |
| yaspy.DataError | Data processing error, such as data type conversion errors, data value out of range |
| yaspy.OperationalError | Database operation error, such as connection disconnect, transaction failure, network timeout |
| yaspy.IntegrityError | Integrity constraint error, such as primary key conflict, foreign key constraint violation |
| yaspy.InternalError | Database internal error, such as server internal exceptions |
| yaspy.ProgrammingError | Programming error, such as SQL syntax error, invalid cursor state, incorrect parameter binding |
| yaspy.NotSupportedError | Unsupported operation, such as calling `nextset()`, `setinputsizes()` and other unimplemented methods |

> **Note**:
>
> The subclasses of `DatabaseError` (`DataError`, `OperationalError`, `IntegrityError`, `ProgrammingError`, etc.) in the table above are only defined for the exception class hierarchy. **The current version of yaspy will not throw these specific types**. All database errors are categorized under `DatabaseError`. If you need to catch specific error types, it is recommended to distinguish them through attributes such as `code` and `message` of `DatabaseError`.

## Exception Handling Example

```python
import yaspy
from yaspy import Error, DatabaseError, InterfaceError

try:
    # Connect to database
    conn = yaspy.connect(dsn="sales/sales@192.168.1.2:1688")

    # Execute SQL
    cursor = conn.cursor()
    cursor.execute("SELECT * FROM example_table")

    # Fetch results
    results = cursor.fetchall()

except InterfaceError as e:
    # Connection parameter error: DSN format error, missing required parameters, etc.
    # Note: Invalid host address or port connection failures usually throw DatabaseError
    print(f"Connection parameter error: {e}")
    print(f"Error code: {e.code}, Message: {e.message}")
except DatabaseError as e:
    # Database error: primary key conflict, SQL syntax error, table not found, etc.
    # In the current version of yaspy, all database operation errors are thrown as this type
    print(f"Database error: {e}")
    print(f"Error code: {e.code}, Line: {e.line}, Column: {e.column}")
    print(f"Message: {e.message}")
except Error as e:
    # General error (covers all error types above)
    print(f"Error: {e}")
    print(f"Error code: {e.code}, Line: {e.line}, Column: {e.column}")
    print(f"Message: {e.message}")
finally:
    # Close connection
    if 'cursor' in locals():
        cursor.close()
    if 'conn' in locals():
        conn.close()
```

The `except DatabaseError` branch in the example above covers the following typical error scenarios:
- Primary key conflict
- SQL syntax error
- Table or view not found
- Transaction failure, network timeout

> **Note**:
>
> `except ProgrammingError` or `except OperationalError` **will not catch** any of the errors above. All database operation errors are caught by `except DatabaseError` / `except Error`. To distinguish error types, use the `e.code` or `e.message` attributes for judgment.

## Exception Handling Recommendations

- Catch specific exceptions (with caution): Since the current version of `yaspy` only throws `DatabaseError` and `InterfaceError`, it is recommended to catch using these two exception types. There is no need to try catching subclasses like `ProgrammingError`, `OperationalError`, or `IntegrityError`.

- Resource cleanup: Use `finally` block to ensure connections and cursors are properly closed to avoid resource leaks.

- Do not ignore exceptions: Avoid using empty `except` blocks, be sure to log or handle caught exceptions.

- Distinguish error types by attributes: To distinguish specific scenarios such as primary key conflicts or SQL syntax errors, use the `e.code`, `e.message` and other attributes for judgment. For common error code mappings, see the corresponding chapter.
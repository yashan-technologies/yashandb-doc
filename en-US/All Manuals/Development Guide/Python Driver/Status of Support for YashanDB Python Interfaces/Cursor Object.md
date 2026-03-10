The cursor object is used to manage operations on specific content in the database, such as executing SQL statements and retrieving execution results.

##  Object Creation Method

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

## Cursor Attributes

|Attribute |Description |
| ----------- |------ |
| Cursor.description: list[FetchInfo] &#124; None | Metadata about query result columns, including column names and types. <br/>Read-only, automatically generated after executing an SQL statement. |
| Cursor.rowcount: int | Number of rows generated or affected by the last execute(). <br/>Read-only, automatically updated after SQL execution. |
| Cursor.arraysize: int | Specifies the number of rows to fetch with fetchmany(). <br/>Writable, e.g., `cursor.arraysize = 100`. |
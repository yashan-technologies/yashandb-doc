The YashanDB Go driver has implemented the following interfaces defined by the Go standard library `database/sql/driver`:

|Interface |Function / Property Name |Function |Description |
| ------------------------------ | -------------------------- | --------------------------------------------------------------------------------- | ---------------------------------------------------- |
| Driver                         | Open                       | Open(name string) (Conn, error)                                                   | Opens a database connection                           |
| Connector                      | Connect                    | Connect(context.Context) (Conn, error)                                            | Returns a Conn to connect to the database.           |
|                                | Driver                     | Driver() Driver                                                                     | Returns the Driver object                             |
| Pinger                         | Ping                       | Ping(ctx context.Context) error                                                    | Probes the database connection                        |
| Conn                           | Prepare                    | Prepare(query string) (Stmt, error)                                               | Prepares a statement that is bound to this connection.|
|                                | Close                      | Close() error                                                                       | Marks the connection as no longer in use             |
|                                | Begin                      | Begin() (Tx, error)                                                                | Starts and returns a new transaction.                |
| ConnPrepareContext             | PrepareContext             | PrepareContext(ctx context.Context, query string) (Stmt, error)                   | Returns a prepared statement on this connection       |
| ConnBeginTx                    | BeginTx                    | BeginTx(ctx context.Context, opts TxOptions) (Tx, error)                          | Supports starting and returning a new transaction with TxOptions. |
| SessionResetter                | ResetSession               | ResetSession(ctx context.Context) error                                            | Resets the connection                               |
| Result                         | RowsAffected               | RowsAffected() (int64, error)                                                     | Returns the number of affected rows                   |
| Stmt                           | Close                      | Close() error                                                                       | Closes the statement                                  |
|                                | Exec                       | Exec(args []Value) (Result, error)                                                | Executes a database operation without returning a result set |
|                                | Query                      | Query(args []Value) (Rows, error)                                                 | Executes a database operation and returns a result set |
|                                |NumInput                    |NumInput() int                    |Returns the number of current SQL placeholder parameters. |
| StmtExecContext                | ExecContext                | ExecContext(ctx context.Context, query string, args []NamedValue) (Result, error) | Executes Exec with context                           |
| StmtQueryContext               | QueryContext               | QueryContext(ctx context.Context, query string, args []NamedValue) (Rows, error)  | Executes Query with context                          |
| NamedValueChecker              | CheckNamedValue            | CheckNamedValue(*NamedValue) error                                                | Checks parameters before passing them to the database |
| Rows                           | Columns                    | Columns() []string                                                                  | Returns the names of the columns                      |
|                                | Close                      | Close() error                                                                       | Closes the Rows iterator                             |
|                                | Next                       | Next(dest []Value) error                                                            | Iterates to the next row of the result set, populating the provided slice with the next row's data |
| RowsNextResultSet               | ColumnTypeScanType         | ColumnTypeScanType(index int) reflect.Type                                        | Returns the mapping type of the column               |
| RowsColumnTypeDatabaseTypeName | ColumnTypeDatabaseTypeName | ColumnTypeDatabaseTypeName(index int) string                                      | Returns the database type name of the column         |
| RowsColumnTypeLength           | ColumnTypeLength           | ColumnTypeLength(index int) (length int64, ok bool)                               | Returns whether the type is variable-length and its type length |
| RowsColumnTypeNullable         | ColumnTypeNullable         | ColumnTypeNullable(index int) (nullable, ok bool)                                 | Returns whether the column can be null               |
| RowsColumnTypePrecisionScale   | ColumnTypePrecisionScale   | ColumnTypePrecisionScale(index int) (precision, scale int64, ok bool)             | Returns whether the type is precise and returns its precision and scale |
| Tx                             | Commit                     | Commit() error                                                                      | Commits the transaction                               |
|                                | Rollback                   | Rollback() error                                                                    | Rolls back the transaction                            |
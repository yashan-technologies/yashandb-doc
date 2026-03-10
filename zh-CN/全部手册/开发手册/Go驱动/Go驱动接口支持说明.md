YashanDB Go驱动已实现Go标准库`database/sql/driver`定义的如下接口：

|  Interface| 函数/属性名称| 函数| 说明|
| ------------------------------ | -------------------------- | --------------------------------------------------------------------------------- | ---------------------------------------------------- |
| Driver                         | Open                       | Open(name string) (Conn, error)                                                   | 打开数据库连接                                       |
| Connector                      | Connect                    | Connect(context.Context) (Conn, error)                                            | 返回连接数据库的Conn。                               |
|                                | Driver                     | Driver() Driver                                                                   | 返回Driver对象                                       |
| Pinger                         | Ping                       | Ping(ctx context.Context) error                                                   | 数据库连接探测                                       |
| Conn                           | Prepare                    | Prepare(query string) (Stmt, error)                                               | 准备返回绑定到此连接的准备好的语句。                 |
|                                | Close                      | Close() error                                                                     | 标记连接不再使用                                     |
|                                | Begin                      | Begin() (Tx, error)                                                               | 启动并返回一个新事务。                               |
| ConnPrepareContext             | PrepareContext             | PrepareContext(ctx context.Context, query string) (Stmt, error)                   | 返回此连接上准备好的statement                        |
| ConnBeginTx                    | BeginTx                    | BeginTx(ctx context.Context, opts TxOptions) (Tx, error)                          | 支持带TxOptions启动并返回一个新事务。                |
| SessionResetter                | ResetSession               | ResetSession(ctx context.Context) error                                           | 重置连接                                             |
| Result                         | RowsAffected               | RowsAffected() (int64, error)                                                     | 返回受影响的行数                                     |
| Stmt                           | Close                      | Close() error                                                                     | 关闭statement                                        |
|                                | Exec                       | Exec(args []Value) (Result, error)                                                | 执行数据库操作，不返回结果集                         |
|                                | Query                      | Query(args []Value) (Rows, error)                                                 | 执行数据库操作，返回结果集                           |
|                                |NumInput                    |NumInput() int                    |返回当前SQL占位符参数的数量。|
| StmtExecContext                | ExecContext                | ExecContext(ctx context.Context, query string, args []NamedValue) (Result, error) | 带上下文执行Exec                                     |
| StmtQueryContext               | QueryContext               | QueryContext(ctx context.Context, query string, args []NamedValue) (Rows, error)  | 带上下文执行Query                                    |
| NamedValueChecker              | CheckNamedValue            | CheckNamedValue(*NamedValue) error                                                | 在传给数据库前进行参数检查                           |
| Rows                           | Columns                    | Columns() []string                                                                | 返回列的名称。                                       |
|                                | Close                      | Close() error                                                                     | 关闭Rows的迭代器                                     |
|                                | Next                       | Next(dest []Value) error                                                          | 遍历结果集下一行数据，将下一行数据填充到提供的列表中 |
| RowsNextResultSet               | ColumnTypeScanType         | ColumnTypeScanType(index int) reflect.Type                                        | 返回该列的映射类型                                   |
| RowsColumnTypeDatabaseTypeName | ColumnTypeDatabaseTypeName | ColumnTypeDatabaseTypeName(index int) string                                      | 返回列的数据库类型名称                               |
| RowsColumnTypeLength           | ColumnTypeLength           | ColumnTypeLength(index int) (length int64, ok bool)                               | 返回是否为可变长类型和其类型长度                     |
| RowsColumnTypeNullable         | ColumnTypeNullable         | ColumnTypeNullable(index int) (nullable, ok bool)                                 | 返回该列是否可以为空                                 |
| RowsColumnTypePrecisionScale   | ColumnTypePrecisionScale   | ColumnTypePrecisionScale(index int) (precision, scale int64, ok bool)             | 返回是否具备精度的类型和精度、小数位数               |
| Tx                             | Commit                     | Commit() error                                                                    | 事务提交                                             |
|                                | Rollback                   | Rollback() error                                                                  | 事务回滚                                             |

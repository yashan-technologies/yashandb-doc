## DbTransaction 类

定义数据库事务的核心行为，并为数据库专用事务提供基类。

- 命名空间: System.Data.Common

### 支持的方法

| 方法             |
| ---------------- |
| Commit()         |
| Dispose()        |
| Dispose(Boolean) |
| Rollback()       |

### 未支持的方法

| 方法 |
| ---------------------------------------- |
| CommitAsync(CancellationToken)           |
| DisposeAsync()                           |
| Release(String)                          |
| ReleaseAsync(String, CancellationToken)  |
| Rollback(String)                         |
| RollbackAsync(CancellationToken)         |
| RollbackAsync(String, CancellationToken) |
| Save(String)                             |
| SaveAsync(String, CancellationToken)     |

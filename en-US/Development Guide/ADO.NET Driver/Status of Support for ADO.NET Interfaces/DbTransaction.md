## DbTransaction Class

Defines the core behaviors of database transactions and provides a base class for database-specific transactions.

- Namespace: System.Data.Common

### Supported Methods

|Method |
| ---------------- |
| Commit()         |
| Dispose()        |
| Dispose(Boolean) |
| Rollback()       |

### Unsupported Methods

|Method |
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

The correspondence is shown in the following table. The SQLState attribute and its error code for exceptions that are not listed or classified are numerically equal.

|Exception |sqlState value |Meaning |
| ---------------------------------------- | ---------- | ------------------------------------------------------------ |
| SQLDataException                          | 22000          | Various data errors, including but not limited to data conversion errors, division by zero, and invalid parameters for functions |
| SQLFeatureNotSupportedException           | 0A000          | Feature not supported exception                               |
| SQLIntegrityConstraintViolationException  | 23000          | Integrity constraint violation, primary key, foreign key, unique key |
| SQLInvalidAuthorizationSpecException      | 28000          | Invalid authorization credentials provided during connection setup, incorrect username or password when logging in |
| SQLNonTransientConnectionException        | 08000          | Non-transient connection exception                             |
| SQLNonTransientException                  | 58000          | Non-transient exception, an exception that will persist without repair |
| SQLRecoverableException                   | 56000          | An exception that can be recovered by closing the current connection and obtaining a new one. |
| SQLSyntaxErrorException                   | 42000          | Syntax error                                                 |
| SQLTimeoutException                       | 57000          | Timeout caused by setQueryTimeout, setLoginTimeout, etc.    |
| SQLTransactionRollbackException           | 40000          | Transient rollback exception, deadlock or other transactional sequence failure, the database automatically rolled back the current statement |
| SQLTransientConnectionException           | 08000          | Transient connection exception                                 |
| SQLTransientException                     | 57000          | Transient exception                                           |
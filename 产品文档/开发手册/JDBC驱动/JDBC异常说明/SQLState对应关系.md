对应关系如下图所示，未列出或者未分类的异常的SQLState属性和它的错误码在数值上相等。

| 异常                                     | sqlState值 | 含义                                                         |
| ---------------------------------------- | ---------- | ------------------------------------------------------------ |
| SQLDataException                         | 22000      | 各种数据错误，包括但不限于数据转换错误、除0以及函数的无效参数 |
| SQLFeatureNotSupportedException          | 0A000      | 特性不支持异常                                               |
| SQLIntegrityConstraintViolationException | 23000      | 完整性约束冲突，主键，外键，唯一键                           |
| SQLInvalidAuthorizationSpecException     | 28000      | 建立连接期间提供的授权凭据无效，登录时用户名密码错误         |
| SQLNonTransientConnectionException       | 08000      | 非瞬态连接异常                                               |
| SQLNonTransientException                 | 58000      | 非瞬态异常，即不修复就会一直出现的异常                       |
| SQLRecoverableException                  | 56000      | 能通过 关闭当前连接和获取新连接等方式恢复的异常。            |
| SQLSyntaxErrorException                  | 42000      | 语法错误                                                     |
| SQLTimeoutException                      | 57000      | setQueryTimeout，setLoginTimeout等导致的超时                 |
| SQLTransactionRollbackException          | 40000      | 瞬态回滚异常，死锁或其他事务序列化失败，数据库自动回滚了当前语句 |
| SQLTransientConnectionException          | 08000      | 瞬态连接异常                                                 |
| SQLTransientException                    | 57000      | 瞬态异常                                                     |

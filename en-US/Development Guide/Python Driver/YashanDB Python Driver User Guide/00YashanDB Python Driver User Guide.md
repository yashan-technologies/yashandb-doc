This chapter describes the advanced functions and features of YashanDB Python driver, helping developers use the driver for database development more efficiently.

This chapter is suitable for developers who have mastered basic Python driver usage and need to use advanced features.

This chapter includes:

- [Python Flask + SQLAlchemy Integration with YashanDB](./Python Flask + SQLAlchemy Integration with YashanDB): Introduces how to quickly integrate YashanDB using Flask + SQLAlchemy, including connection pool configuration, performance optimization, and common problem handling

- [Handling Transactions](./Python Transaction Management): Introduces transaction commit, rollback, and isolation level operations, including auto-commit mode switching, manual commit, transaction rollback, savepoint operations, and isolation level configuration such as READ COMMITTED and SERIALIZABLE. YashanDB does not support READ UNCOMMITTED and REPEATABLE READ isolation levels.

- [Handling BLOB and CLOB](./Python BLOB and CLOB Handling): Introduces operations for large objects such as BLOB and CLOB, including insertion, reading, updating, and deletion of binary data and text large objects

- [Calling Stored Procedures](./Python Stored Procedure Calling): Introduces methods for calling YashanDB stored procedures and functions using Python driver, supporting IN, OUT, and INOUT parameter types

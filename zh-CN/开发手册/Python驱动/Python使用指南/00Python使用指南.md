本章节介绍YashanDB Python驱动的高级功能和特性，帮助开发者更高效地进行数据库开发。

本章节适合已掌握Python驱动基础用法，需要使用高级特性的开发者。

本章节包含以下内容：

- [Python Flask + SQLAlchemy集成YashanDB](./Python Flask + SQLAlchemy集成YashanDB.md)：介绍如何使用Flask + SQLAlchemy快速集成YashanDB，包含连接池配置、性能优化和常见问题处理

- [处理事务](./Python处理事务.md)：介绍事务的提交、回滚和隔离级别操作，包括自动提交模式切换、手动提交、事务回滚、保存点操作以及READ COMMITTED、SERIALIZABLE隔离级别配置。YashanDB不支持READ UNCOMMITTED和REPEATABLE READ隔离级别。

- [处理BLOB和CLOB](./Python处理BLOB和CLOB.md)：介绍BLOB和CLOB等大对象的操作方法，包括二进制数据和文本大对象的插入、读取、更新和删除

- [调用存储过程](./Python调用存储过程.md)：介绍使用Python驱动调用YashanDB存储过程和函数的方法，支持IN、OUT、INOUT参数类型

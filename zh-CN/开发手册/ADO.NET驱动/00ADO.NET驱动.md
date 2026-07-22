ADO.NET是用于.NET语言的核心数据访问技术。 该技术定义了一套对公开数据访问服务的类和接口，面向开发者提供了对关系数据、XML 和应用程序数据的一致访问，因此成为.NET Framework中不可缺少的一部分。

YashanDB ADO.NET驱动使用Yashandb.Data.YashandbClient命名空间，实现了ADO.NET的大部分类和接口，使得.NET的应用程序可通过ADO.NET的方式访问和操作YashanDB。

YashanDB ADO.NET驱动仅支持连接部署为yashan模式的YashanDB。

YashanDB ADO.NET驱动是YashanDB对标准的ADO.NET的实现，包括下列类，其具体实现请查阅[ADO.NET接口支持说明](ADO.NET接口支持说明/00ADO.NET接口支持说明)：

- DbConnection：ADO.NET客户端到数据库的连接。
- DbTransaction：定义数据库事务的核心行为，并为数据库专用事务提供基类。
- DbCommand：向数据库发送SQL命令，及从数据库获取结果集。
- DbParameter：SQL绑定参数方法类。
- DbDataReader：结果集获取方法类。
- DbParameterCollection：与DbCommand相关的参数集合的基类。

YashanDB ADO.NET驱动允许多个线程同时共享同一个Connection对象，采用串行化执行机制（即同一时刻仅允许一个线程执行数据库操作）避免竞态条件和数据竞争问题，从而保证数据一致性和操作原子性。为避免资源泄漏，建议：

- 在执行完业务逻辑后，显式调用Command、DataReader等对象的Dispose()方法（或配合using 语句）释放手动申请的系统资源。

- 在断开连接后，显式调用Connection.Dispose()方法释放连接资源。
  
>**Caution**:
>
> 调用Command.Dispose()与调用Connection.Dispose()无严格的先后顺序约束，但调用Connection.Dispose()方法后无法再通过该连接对象执行Command操作（例如执行SQL语句、存储过程等），请遵循业务逻辑合理安排资源的释放时机和顺序。
>
> YashanDB ADO.NET驱动已针对资源重复释放场景实现了完善的保护机制，确保多次调用Dispose()不会导致未定义行为。

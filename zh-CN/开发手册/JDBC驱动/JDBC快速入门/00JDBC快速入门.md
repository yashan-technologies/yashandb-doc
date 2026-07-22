JDBC（Java Database Connectivity）是Java语言操作关系型数据库的标准API（Application Programming Interface），由Sun公司于1997年发布，现已成为Java标准库的一部分。JDBC提供了一套统一的接口规范，使得Java应用程序能够通过相同的方式连接和操作各种不同的数据库，如MySQL、Oracle、PostgreSQL以及YashanDB等。

在JDBC出现之前，Java程序员需要为每种数据库编写特定的代码，这导致了严重的可移植性问题。JDBC的出现彻底解决了这一难题——开发者只需使用JDBC标准API编写代码，无需关心底层数据库的具体实现细节。当需要切换数据库时，只需更换对应的JDBC驱动即可，极大地提高了代码的可移植性和开发效率。

JDBC的工作原理建立在分层架构之上。应用程序通过JDBC API发送数据库操作请求，JDBC驱动管理器（DriverManager）根据数据库URL选择合适的驱动，驱动再将请求转换为数据库能识别的协议并执行。整个过程对开发者透明，开发者只需关注业务逻辑的实现。

JDBC API包含多个核心接口和类，它们协同工作来完成数据库操作：

- **DriverManager**：驱动管理器，负责加载JDBC驱动并建立数据库连接。

- **Connection**：表示与数据库的连接会话，用于创建Statement对象。

- **Statement**：用于执行静态SQL语句并返回结果。

- **PreparedStatement**：预编译的SQL语句，支持参数化查询，防止SQL注入。

- **ResultSet**：表示SQL查询返回的结果集，提供遍历和读取数据的方法。

典型的JDBC使用流程包括以下步骤：

1. 加载JDBC驱动。

2. 通过DriverManager建立数据库连接。

3. 创建Statement或PreparedStatement对象。

4. 执行SQL语句并处理结果。

5. 关闭ResultSet、Statement和Connection资源。

这篇入门教程会带你快速掌握JDBC核心用法，涵盖数据库连接、资源关闭和增删改查（CRUD）全流程，代码可直接运行。

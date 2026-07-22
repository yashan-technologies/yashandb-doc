在Python开发领域，数据库操作是绝大多数企业级应用、后端服务的核心需求——从用户信息存储、交易记录留存，到海量数据统计分析，都离不开Python程序与数据库的高效交互。而YashanDB Python驱动，作为Python访问YashanDB数据库的官方标准，正是打通Python程序与YashanDB数据库之间壁垒的关键桥梁。

YashanDB Python驱动（python-yaspy）是YashanDB官方提供的Python数据库驱动程序，遵循Python DB API v2.0规范，是支持[Python DB API协议](https://peps.python.org/pep-0249/)的Python拓展模块，该模块可使通用Python应用程序直接连接YashanDB数据库，为Python应用程序提供访问YashanDB数据库的能力。

其核心本质是：为Python程序员提供统一的编程接口，屏蔽不同数据库的底层实现差异，让开发者无需针对不同数据库编写不同的访问代码，只需遵循Python DB API规范，就能通过统一的代码操作YashanDB数据库。

仅当YashanDB部署为yashan模式时支持使用该驱动进行连接。

YashanDB提供两个python驱动包：yaspy、yasdb，安装其一后即可连接YashanDB数据库。

YashanDB v23.4.4对yaspy模块进行了性能提升（同等测试环境下，yaspy较yasdb有较大性能优势）。此外，后续新增功能的演进优先级为yaspy > yasdb，推荐使用**yaspy**模块。

本章节仅以yaspy为例，yasdb模块可进行参考。

YashanDB Python驱动支持以下特性：

- **数据库连接管理**：支持独立连接和连接池两种模式
- **SQL执行**：支持执行DDL、DML等SQL语句
- **事务管理**：支持事务的提交和回滚
- **参数化查询**：支持命名参数（`:name`）和位置参数（`?`）
- **连接池**：支持SessionPool连接池，提高高并发场景下的性能

## 技术规格

| 规格项| 说明|
| --------------- | -------------------- |
| 支持语言 | Python 3.6+ |
| 支持平台 | Windows、Linux |
| API规范 | Python DB API v2.0 |
| 支持的连接方式 | 独立连接、连接池 |
| 参数风格 | named, positional |

## Python驱动解决的问题

1. **简化数据库操作**：通过统一的API接口，开发者可以使用相同的代码风格执行SQL语句、获取查询结果，无需关心底层数据库的通信细节。

2. **提高开发效率**：内置连接池、参数化查询、事务管理等高级功能，减少重复代码编写。

3. **保障程序稳定性**：提供完善的异常处理机制和连接管理，帮助开发者规避连接泄露、操作失败等问题。

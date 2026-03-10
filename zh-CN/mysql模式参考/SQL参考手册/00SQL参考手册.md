
SQL（Structured Query Language）是开发者操作数据库的主要接口，本手册从数据类型、运算符、函数、SQL语句等方面全面介绍YashanDB对于SQL功能的实现。



## 手册中使用的示例说明

* 连接YashanDB服务端（mysql模式）的工具除了YashanDB客户端（yasql）、YashanDB JDBC驱动、YashanDB C驱动外，还支持MySQL的生态周边配套工具，例如MySQL客户端、MySQL Connector/J等。考虑到yasql工具与MySQL Client或其他第三方工具在TIME、TIMESTAMP、DATETIME、NUMBER和DOUBLE数据类型的打印显示区别，本章节示例中的SQL语句如无特别说明将以yasql工具执行，并以yasql工具的输出结果作为样例进行展示，对于差异点将单独列举说明。

* 示例中使用DBMS_OUTPUT.PUT_LINE语句向yasql工具端打印输出，为达到输出效果，需要保证在yasql工具里已运行set serveroutput on来打开控制输出的开关，详见工具手册[yasql使用指导](../../全部手册/工具手册/yasql/yasql使用指导)。

* 使用mysql模式的示例前，需先通过CREATE USER和GRANT语句创建用户并赋权，并执行CREATE DATABASE语句创建数据库实例schema并通过USE语句指定schema。本章节如无特别说明，将以用户sales以及同名数据库实例sales为例进行介绍。

* 系统对浮点类型及NUMBER类型数据输出时的显示宽度默认为10，可通过yasql工具的[set NUMWIDTH](../../全部手册/工具手册/yasql/yasql使用指导.html#numwidth)命令调节显示宽度。

* 关于日期的显示格式：为尽量模拟实际业务，本手册示例库的日期格式被设置为'YYYY-MM-DD HH24:MI:SS'，如DATE_FORMAT配置参数不是按此格式设置，所看到的日期格式将与本手册不一致。

* 关于示例中使用的通用表：本手册使用sales作为示例用户（参考[CREATE USER](SQL语句/CREATE USER)创建用户），同时在该用户下创建一套样例表（样例表来源于下面所列的一套极简化的业务模型，并依据不同架构/存储特性进行相应调整）。示例中使用的通用表即来自于这套样例表，当执行示例时，切换到sales用户下将可以查询到这些表，产品文档中[附：样例表](附：样例表)展示了这些表的创建脚本。


    
    > **Note**: 
    >
    > 为达到特定目的，示例可能需要创建其他表或者修改通用表的结构、数据，这些将不在此处体现，而是查看具体示例。

### 样例业务模型

![](./image/simple_model.png)

```sql
--区域信息表：area、area1
--机构信息表：branches、branches1
--部门信息表：department
--员工信息表：employees
--产品信息表：product
--订单信息表：orders_info
--销售信息表：sales_info、sales_info_range、sales_info_list、sales_info_hash
--财务信息表：finance_info
```

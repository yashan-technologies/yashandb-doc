YashanDB Python驱动（python-yasdb）是支持[Python DB API协议](https://peps.python.org/pep-0249/)的Python拓展模块，该模块可使通用Python应用程序直接连接YashanDB数据库。

YashanDB的产品安装包中提供两个python驱动包：yasdb、yaspy。开发人员安装其一后即可连接YashanDB数据库进行访问操作（YashanDB23.2之后不再对yaspy模块进行功能扩展，建议用户安装使用yasdb模块）。本章所列内容仅以yasdb为例，yaspy模块可进行参考。

模块包含如下内容：

- connect()：创建数据库连接的构造函数。
- Globals：模块中定义的变量。
- Connection：Python应用程序到数据库的连接对象。
- Cursor：连接数据库后创建的游标对象。 

对以上内容的具体描述见[YashanDB Python驱动支持说明](YashanDB Python驱动支持说明/00YashanDB Python驱动支持说明)章节。

具体安装步骤见[安装YashanDB Python驱动](安装YashanDB Python驱动)章节中描述。

查看[YashanDB Python驱动使用介绍](YashanDB Python驱动使用介绍)章节可以了解在Python应用程序中连接并操作YashanDB的简单过程。

本手册还提供了YashanDB Python驱动的简单示例，具体见[YashanDB Python驱动使用示例](YashanDB Python驱动使用示例)。

Python与YashanDB之间的数据类型转换，具体见[YashanDB Python驱动数据类型映射关系](YashanDB Python驱动数据类型映射关系)章节描述。
通用描述
----

CREATE PACKAGE用于创建一个高级包（Package）。高级包是多个PL对象的集合，依据一定业务逻辑将这些对象封装在一个高级包里，便于对内的管理和对外的接口简洁。

高级包也是一个可持久化的PL对象，由如下两部分组成：

- PACKAGE HEAD：通过CREATE PACKAGE语法创建，包含集合中有哪些PL对象的声明。
- PACKAGE BODY：通过CREATE PACKAGE BODY语法创建，包含在PACKAGE HEAD中声明的每个PL对象对应的过程体定义，也可以声明并定义该PACKAGE BODY私有的过程体对象。

对高级包的创建需采用过程体语言实现，且需满足YashanDB所实现PL（包括变量、参数、语句等）的一切规范和约束，具体描述请查看[PL参考手册](../../PL参考手册/00PL参考手册)。

关于CREATE PACKAGE的语法描述请查阅[自定义高级包](../../PL参考手册/PL对象/自定义高级包)。

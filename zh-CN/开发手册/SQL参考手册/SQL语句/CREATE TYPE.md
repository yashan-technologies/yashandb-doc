通用描述
----

CREATE TYPE用于创建或替换一个数据库自定义类型（UDT）， 相对于数据库内置的数据类型而言，UDT是由用户自定义的数据类型。

在YashanDB中，用户可以定义如下类型的UDT：

- Object：抽象数据类型（Abstract Data Type），该类UDT由属性和方法组成，创建此类UDT必须同时使用[CREATE TYPE BODY](./CREATE TYPE BODY)语句构建方法的具体实现。
- Varray：数组类型，该类UDT为一组相同数据类型的集合。

用户需要拥有CREATE TYPE系统权限，才能在自己的模式下创建自定义类型。

用户需要拥有CREATE ANY TYPE系统权限，才能在任何模式下创建自定义类型。

对自定义类型的创建需采用过程体语言实现，且需满足YashanDB所实现PL（包括变量、参数、语句等）的一切规范和约束，具体描述请查看[PL参考手册](../../PL参考手册/00PL参考手册)。 

关于CREATE TYPE的语法描述详见PL参考手册的[自定义类型](../../PL参考手册/PL对象/自定义类型)。

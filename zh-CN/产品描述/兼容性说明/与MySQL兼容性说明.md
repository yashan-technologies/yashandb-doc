在崖山数据库的单机（主备）以及共享集群环境中，提供了MySQL数据库的兼容性功能，但由于不同数据库的底层架构、产品形态等存在差异，导致适配MySQL数据库的某些特性与适配Oracle数据库的某些特性不能互相兼容。YashanDB通过控制开关的方式，很好地解决了这个问题，从而可以让用户从不同数据库向YashanDB迁移时，无需进行大量地SQL校对和改写工作。

YashanDB通过语法模式选择决定兼容MySQL适配度，用户在安装数据库时可按需选择：

- [mysql模式](../../mysql模式参考手册/mysql模式概述.md)：在该模式下用户输入的SQL语句将优先按照MySQL的语法体系进行解析，适用于需要密切适配MySQL数据库的使用场景。
- yashan模式：在该模式下使用YashanDB的语法体系。若安装时指定为yashan模式（省略不指定时，默认为yashan模式），安装后无法切换为mysql模式。


在mysql模式下，用户输入的SQL语句将优先按照MySQL的语法体系进行解析，还支持MySQL专有语句，例如show语句、use语句等。

## 控制开关

在YashanDB安装过程中，通过yasboot package se gen命令的mode参数可以指定语法模式为mysql模式。

以mysql模式安装后，在数据库OPEN阶段新建的所有会话默认为mysql模式，在NOMOUNT或MOUNT阶段创建的会话则仍为yashan模式，可通过以下方式查看当前会话的语法模式。

```sql
-- 查询COMPAT_VECTOR参数值
SHOW PARAMETER COMPAT_VECTOR

name                                                             value                                                  
---------------------------------------------------------------- ----------------------------------------------------------------
COMPAT_VECTOR                                                    mysql            
```

在mysql模式下，部分数据库管理操作（例如主备切换）无法正常执行，需要先将当前会话切换至yashan模式（不影响其他会话）再执行语句/命令，切换方式如下：

```sql
-- 切换到yashan模式，再执行相关运维操作
ALTER SESSION SET COMPAT_VECTOR = yashan;

-- 运维操作完成后，再切换回mysql模式继续进行业务相关操作
ALTER SESSION SET COMPAT_VECTOR = mysql;
```

## 概念介绍

|  名词| yashan模式（同Oracle）| mysql模式|
|--------------------|-----------------------|---------------|
| 数据库（Database） | 由表空间、数据文件等组成的完整实例<br/>一套YashanDB环境就是1个Database | 数据库对象（表、视图等）的逻辑容器 |
| 模式（Schema） | 与用户一一对应，是用户的数据库对象（表、视图等）的逻辑容器<br/>创建用户时，自动创建1个同名schema | 数据库的别名  |
| 用户（User） | 登录账号 + 同名模式拥有者（天然具备该模式下对象的全部权限）<br/>可被授予其他模式对象的权限 | 仅为登录账号<br/>可被授予任意数据库的权限 |
| 角色（Role） | 权限的集合 | 无<br/>可以使用YashanDB内置的角色（使用时基于yashan语法解析相关SQL语句） |

## 具体兼容项

### 通信协议兼容



YashanDB安装为mysql模式时，会默认开启单独的监听端口，处理通过MySQL协议向YashanDB服务端发起的连接、执行等协议命令。



### 语法兼容



在YashanDB的语法体系中，SELECT语句必须包含FROM子句，而MySQL的语法允许SELECT语句不带FROM子句。切换成mysql模式后，执行以下语句，将返回当前会话的登录用户名，而不是报错：

```SQL
SELECT USER();
```

- **词法**

    例如mysql模式下，反引号（`）表示对象名，单引号（'）表示字符串，双引号（"）默认表示字符串，但在sql_mode中包含ANSI_QUOTES时，表示对象名。而yashan模式下，双引号表示对象，单引号表示字符串。

- **对象的概念**

    例CREATE DATABASE语句，在mysql模式下表示创建一个Schema。

- **数据类型**

    例如Bool类型，在mysql模式下表示Tinyint(1)。

- **字面量的数据类型**

    字符类型的字面量，在mysql模式下，其数据类型为VARCHAR（与MySQL兼容），在yashan模式下，类型为CHAR（与Oracle兼容）。

-  **函数**

    在原有YashanDB的基础上，同步了100+个与MySQL的同名函数；同时新增实现了80多MySQL独有的内置函数，涵盖时间日期、字符处理、数学运算、流程控制、系统信息、密码校验等函数。

- **数据定义语言**
  
    例如CREATE TABLE语句，在mysql模式下会兼容Engine、Character set等选项，不再支持pctfree等选项。

- **数据操作语言**
  
    例如mysql模式下支持SELECT语句不带FROM子句。

- **权限**

    例如执行SELECT FOR UPDATE语句，在mysql模式下需要用户具有表的读权限，以及插入、删除、更新三者之中任意一种权限。而在yashan模式下，SELECT语句需要读（READ）权限，SELECT FOR UPDATE语句需要单独的SELECT权限。

- **字符序**

    mysql模式下，字符类型的比较、排序规则受字符序影响（例如，数据库字符集选择为UTF8MB4时，默认的字符序为UTF8MB$_GENERAL_CI，字符类型的排序和比较不区分大小写、忽略末尾空格），而在yashan模式下，则按照二进制的方式比较和排序（区分大小写、末尾空格参与比较）。

> **Caution**:
>
> - YashanDB 23.4暂未覆盖MySQL的所有语法及行为，对于暂未覆盖的那部分语句，在mysql模式下执行时仍会按照YashanDB语法进行解析和执行。
>
>- 对于MySQL本身不支持的YashanDB功能，这类语句在mysql模式下并不会产生歧义，因此在mysql模式下仍可以按照YashanDB语法进行解析和执行，例如查询语句的集合操作（MINUS、INTERSECTS）、层次查询（CONNECT BY）。



### 保留字兼容



YashanDB兼容了MySQL数据库大部分保留字，但目前个别保留字仍存在一定区别，包括：

- CONNECT
- EXCEPT
- IF
- INTERSECT
- MINUS
- OF
- PUBLIC
- ROW
- ROWS
- START
- SYSDATE



## 规格差异

YashanDB数据库在mysql模式下，绝大多数规格与yashan模式保持一致，以下仅列出与yashan模式下存在差异的规格。



### 对象规格

|  规格名称| 规格类型| 最大值|
| ------------------- | -------- | ------------------------------------------ |
| user数量            | 最大值   | 10240 - 内置user数量 - database/schema数量 |
| database\schema数量 | 最大值   | 10240 - 内置user数量 - 普通user数量        |
| user名称长度        | 最大值   | 60Bytes                                    |

### 数据类型规格

- 完成整型、浮点型、DECIMAL、BOOLEAN、日期时间类型、定长/变长字符/二进制类型、国家字符集类型、BLOB/TEXT类型和MYSQL的全面兼容。

- 支持MySQL专有的无符号类型。

- 新增float/double浮点数精度同步。

- 字符类型规格提升：字符/二进制类型最大规格提升至65534。

- 支持MySQL隐式转换规则，同步了时间日期类型、字符类型、二进制类型、浮点型间的隐式转换逻辑。



详情请查阅[数据类型（mysql模式）](../../mysql模式参考手册/SQL参考手册/数据类型/00数据类型.md)。

## 功能约束

使用mysql模式时，存在如下约束：

|  约束项| 约束行为|
| ----------------------- |-------------------------------------------|
| 部署形态       |仅单机或单机主备部署时可选为mysql模式。                                       |
| 表类型                  | mysql模式下仅支持创建行存表。                           |
| 用户登录                | * 如需使用yashan模式下创建的用户登录YashanDB（mysql模式），则要求客户端支持SHA256插件。<br/>* 如需使用yasql以mysql模式下创建的用户登录YashanDB（mysql模式）需要使用双引号将用户名包围。                                   |
| 用户删除和修改          | 不允许在mysql模式下删除和修改yashan模式下创建的用户。         |
| 对象名称                | lower_case_table_names参数仅支持设置为1。<br/>schema、表、视图、表别名的名称按照小写展示，列名按照大写展示。<br/>对象名称匹配按照大小写不敏感的方式匹配。             |
| Binary/Blob类型文本协议 | 当客户端与服务端字符集不一致时，会按服务端字符集转码，可能与客户端预期不一致。      |
| 字符集                  | schema/table/column级的字符集必须与实例级字符集设置一致。 |
| 排序集                  | schema/table/column均可指定字符序且支持实际功能，必须与实例级字符集兼容。 |
| 全局变量                | 全局变量查询和设置仅为语法兼容，除字符集、自动提交外，大部分实际不会生效。     |
| SQL_MODE                | YashanDB mysql模式已实现了部分SQL_MODE语法开关，例如ANSI_QUOTES、NO_AUTO_VALUE_ON_ZERO和NO_BACKSLASH_ESCAPES等，详细兼容项说明请参考[SQL_MODE](../../mysql模式参考手册/SQL参考手册/SQL_MODE.md)。对于未完成实际能力兼容的开关，无论如何修改参数值，均不会对数据库的行为产生影响，实际执行效果与yashan模式一致。 |
| 可执行注释              | 可执行注释在mysql模式下会被视为注释，对语句执行不产生影响。      |

通用描述
----

CREATE DIRECTORY用于新建数据目录对象。具有create any directory权限的所有用户均可执行本语句创建数据目录对象，但目录对象始终为sys用户所有。


语句定义
----

**create directory::=**

```ebnf+diagram
syntax::= CREATE [OR REPLACE] DIRECTORY directory_name AS "path_name"
```

### 1. directory\_name

该语句用于指定创建的目录的名称，不可省略，且需符合YashanDB的[对象命名规范](../基本SQL元素/标识符)。

### 2. path\_name

该语句用于指定创建的目录的地址。支持指定本地文件路径与YFS文件路径（YFS文件路径参见[YFS文件管理](../../../共享集群/集群文件系统/文件管理)）。

该语句需满足如下规则：

- 长度限制为4000。
- 其中不可以包含父目录(..)。
- 创建目录时不检查目录是否真实存在。

示例

```sql
CREATE DIRECTORY dir AS '/data/yashan';
```

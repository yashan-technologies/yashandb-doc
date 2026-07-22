imp是YashanDB的配套导入工具，提供以文件方式进行数据导入的能力。

元数据文件由[exp](../exp/00exp)工具生成，包含表结构、索引、约束等所有的元信息和数据，可用于同构数据库之间的数据迁移或备份恢复。

本工具支持FULL（导入整个文件）、FROMUSER（导入指定用户）、TABLES（导入指定的表）等多种模式的导入。

可以被imp工具进行元数据导入的对象有：

- 用户
- SEQUENCE
- AC
- 同义词
- 视图/物化视图
- 表（含虚拟列的表除外）
- 索引/分区索引
- 主键
- 外键
- 审计策略/使能
- 权限
- PROFILE
- OUTLINE
- PACKAGE(BODY)/PROCEDURE/FUNCTION/TRIGGER/JOB/LIBRARY/TYPE(BODY)
- DATABASE LINK

同时，对象的约束、依赖关系（例如VIEW依赖的TABLE）、列属性等信息也将被导入。

> **Note**:
>
> - 对于存在UDT列的表，元数据导入的限制如下：
>   - 对于引用TYPE的表，导入时会返回警告，但仍继续导入。
>   - TABLES模式下，不会导入间接使用的TYPE。
>   - FROMUSER或TABLES模式下，导入的表所使用的不同用户下的TYPE不存在时，会返回警告，但仍继续导入。
> - 导入语句长度限制：当导入语句超出2M（例如视图创建语句）会出现警报，此对象无法导入，但不阻断导入流程。

## 权限要求

imp工具的执行命令里要求输入数据库用户/密码。

在导入元数据文件时，该用户为执行导入的操作用户，而非导入对象，对此操作用户的权限规则为：

- 导入本用户下的对象时，无权限要求。
- 导入其他用户下的对象时，必须拥有DBA角色。

示例

```sql
-- 将DBA角色授予sales用户
GRANT DBA TO sales;

-- 导入sale0用户
imp sales/sales file=export.dump fromuser=sales0

-- 导入sale0用户下的表
imp sales/sales file=export.dump tables=area fromuser=sales0
```

> **Note**: 
>
> 高版本imp工具暂不支持连接低版本的数据库进行导入，请使用版本兼容的工具进行数据迁移操作。

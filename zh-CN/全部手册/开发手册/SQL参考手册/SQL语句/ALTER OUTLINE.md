通用描述
----

ALTER OUTLINE用于修改一个存储纲要，包括重建，更改名称，更改归属类别，设置为有效，设置为无效等操作。

用户必须拥有ALTER ANY OUTLINE权限才能修改一个存储纲要。

在存算一体分布式集群部署中，执行本语句时系统会检查是否存在异常未恢复DDL语句，若存在将报错并需等待对应DDL恢复成功后才能执行本语句。

语句定义
----

**alter\_outline::=**

```ebnf+diagram
syntax::= ALTER [PUBLIC] OUTLINE outline_name (REBUILD|RENAME TO new_outline_name|CHANGE CATEGORY TO category_name|(ENABLE | DISABLE | SET ID UPGRADE))
```

### 1. PUBLIC

公有模式，默认值。

### 2. outline\_name

将要修改的OUTLINE的名称。

### 3. REBUILD

OUTLINE进行重建，即重新生成对应的hint信息。

该语法不适用于SQL_ID语法OUTLINE。

### 4. RENAME TO new\_outline\_name

对OUTLINE重命名。重命名的OUTLINE名称不能带模式名指定，且需符合YashanDB的[对象命名规范](../基本SQL元素/标识符)。

### 5. CHANGE CATEGORY TO category\_name

更改OUTLINE对应的类别。类别名称可以是已经存在的或者新指定的，不能带模式名指定，且需符合YashanDB的[对象命名规范](../基本SQL元素/标识符)。

该语法不适用于SQL_ID语法OUTLINE。

### 6. ENABLE

允许使用该OUTLINE。

### 7. DISABLE

不允许使用该OUTLINE。

### 8. SET ID UPGRADE

该语法仅用于数据库升级流程，由升级脚本自动执行，用户不可使用。

示例

```sql
-- 重建OUTLINE
ALTER OUTLINE ol_a REBUILD;

-- 更改OUTLINE的归属类别，系统自动创建不存在的类别
ALTER OUTLINE ol_a CHANGE CATEGORY TO ctgy_new;

-- 禁止使用OUTLINE
ALTER OUTLINE ol_a DISABLE;

-- 取消OUTLINE的禁用状态
ALTER OUTLINE ol_a ENABLE;
```

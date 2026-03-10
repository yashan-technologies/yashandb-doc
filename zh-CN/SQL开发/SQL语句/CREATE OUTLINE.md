通用描述
----

CREATE OUTLINE用于创建存储纲要，为特定的SQL语句指定hint信息。

创建方式可分为基于SQL_TEXT（即具体的SQL语句内容statement）创建或基于SQL_ID（即具体SQL语句的ID值）创建。

使用OUTLINE信息时，会优先使用基于SQL_TEXT创建的存储纲要，匹配不到时才会使用基于SQL_ID创建的存储纲要。当某个SQL语句存在OUTLINE信息时，优化器在非例外的情况下将会忽略原语句中的hint信息直接依据OUTLINE生成执行计划。OUTLINE可用于固化特定SQL语句的执行计划，使其不受运行环境、统计信息波动等外界因素干扰。因此，OUTLINE可作为特定场景下的性能调优工具，更多详细信息参考[SQL调优原理与规则](../../全部手册/性能调优/SQL调优/00SQL调优)。

用户必须拥有CREATE ANY OUTLINE权限才能创建一个存储纲要。

在存算一体分布式集群部署中，执行本语句时系统会检查是否存在异常未恢复DDL语句，若存在将报错并需等待对应DDL恢复成功后才能执行本语句。

语句定义
----

**create\_outline::=**

```ebnf+diagram
syntax::= CREATE [OR REPLACE] [PUBLIC] OUTLINE [outline_name] (([FROM [PUBLIC] source_outline] [FOR CATEGORY category_name] [ON statement]) | ON sql_id USING HINT hint)
```

### 1. OR REPLACE

替换已有的OUTLINE。

### 2. PUBLIC

公有模式，默认值。

### 3. outline\_name

将要创建的OUTLINE的名称，不能与已有OUTLINE名称重复，不能带模式名指定，且需符合YashanDB的[对象命名规范](../../全部手册/开发手册/SQL参考手册/基本SQL元素/标识符)。

若省略，系统将根据当前时间自动生成一个OUTLINE名称。

### 4. FROM source\_outline

使用本语句表示从一个已有的OUTLINE进行复制创建。

对于此种方式创建的OUTLINE，必须为其指定一个与源OUTLINE不同的类别，且不能同时使用ON statement。

### 5. FOR CATEGORY category\_name

指定OUTLINE归属的类别，省略此语句时，系统将默认归属到DEFAULT类别。

category_name为类别的名称，可以为已存在的一个类别名，或者为新指定的一个类别名。category_name不能带模式名指定，且需符合YashanDB的[对象命名规范](../../全部手册/开发手册/SQL参考手册/基本SQL元素/标识符)。

对于同一个SQL语句，不能将其重复指定到同一个类别。

### 6. ON statement

指定OUTLINE对应的SQL语句。SQL语句必须为如下类型中的一种且需遵循相应的语法要求：

- SELECT语句
- DELETE语句
- UPDATE语句
- INSERT...SELECT语句

对于违反语法要求的SQL语句，OUTLINE将创建失败且提示错误信息。

### 7. sql\_id

指定OUTLINE对应的SQL语句ID值，详细信息请查阅[V$SQL](../../全部手册/参考手册/系统视图/动态视图/V$SQL)。

### 8. hint

指定OUTLINE保存的hint内容，格式上需要符合YashanDB的[hint规范](../../全部手册/开发手册/SQL参考手册/通用SQL语法/hint)。

示例

```sql
-- 使用SQL_TEXT语法对给定的SQL创建OUTLINE。
CREATE OUTLINE ol_a FOR CATEGORY ctgy_ab ON 
SELECT /*+ FULL(a) */ a.area_name
FROM area a
WHERE a.area_no LIKE '01';

-- 对给定的源outline 进行复制，且归属到默认的DEFAULT类别 
CREATE OUTLINE ol_a1 FROM ol_a;

-- 使用SQL_ID语法创建OULINE
CREATE OUTLINE ol_s1 ON '6syjr41dya828' USING HINT /*+ FULL(a) */;
```

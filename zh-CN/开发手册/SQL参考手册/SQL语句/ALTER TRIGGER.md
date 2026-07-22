通用描述
----

ALTER TRIGGER语句用于更改一个已存在的触发器的相关属性或显式的重编译一个触发器。

对于在SYS schema内的触发器，需要由SYS用户执行ALTER TRIGGER语句。

对于其他schema内的触发器，需要由其所属用户或拥有ALTER ANY TRIGGER权限的用户执行ALTER TRIGGER语句。

触发器含义及限制请参考[触发器](../../PL参考手册/PL对象/触发器)章节。

语句定义
----

**alter_trigger ::=**

```ebnf
= ALTER TRIGGER [schema "."] trigger_name
  (trigger_compile_clause
  | (ENABLE | DISABLE)
  | RENAME TO new_name
  | (EDITIONABLE | NONEDITIONABLE)).
```

### schema

包含触发器的模式名称，省略则默认为当前登录用户的模式。

### trigger\_name

需要更改或重编译的触发器的名称。

### trigger\_compile\_clause

指定重编译选项，详见[compile_clause](../通用SQL语法/compile_clause)描述。

### ENABLE|DISABLE

启用或禁用触发器。

### RENAME TO new_name

修改触发器的名称。  

修改触发器名称时，指定的新名称不能为空且必须符合YashanDB的[对象命名规范](../基本SQL元素/标识符)。

### EDITIONABLE | NONEDITIONABLE

用于语法兼容，无实际含义。

示例（单机/共享集群/分布式集群部署）

```sql
-- 显式重编译sales模式的tri触发器
ALTER TRIGGER sales.tri COMPILE;

-- 禁用sales模式的tri触发器
ALTER TRIGGER sales.tri DISABLE;

-- 启用sales模式的tri触发器
ALTER TRIGGER sales.tri ENABLE;

-- 重命名sales模式的tri触发器
ALTER TRIGGER sales.tri RENAME TO newTri;
ALTER TRIGGER sales.newTri RENAME TO tri;
```

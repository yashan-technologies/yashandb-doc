通用描述
----

CREATE SYNONYM用于为一个数据库对象创建一个同义词对象。用户可以在SQL语句中使用同义词来代替原始对象。

存算一体分布式集群部署中用户无法执行本语句。

语句定义
----

**CREATE SYNONYM::=**

```ebnf+diagram
syntax::= CREATE [OR REPLACE] [EDITIONABLE|NONEDITIONABLE] [PUBLIC] SYNONYM [schema "."] synonym FOR ([schema "."] object | object"@"database_link_object)
```

### 1. OR REPLACE

该语句用于指定当要创建的同义词已经存在时，将其进行重建。

### 2. PUBLIC

该语句用于指定同义词为公共同义词，即所有用户都能访问该同义词，此时不能再为同义词指定schema属主。

本语句可省略，默认为私有同义词，则执行schema语句描述规则。

### 3. EDITIONABLE | NONEDITIONABLE

用于语法兼容，无实际含义。

### 4. schema

该语句用于指定同义词的所有者，即用户名。可省略，则默认所有者为当前用户。

### 5. synonym

该语句用于指定要创建的同义词的名称，不可省略，且需符合YashanDB的[对象命名规范](../基本SQL元素/标识符)。

### 6. object

该语句用于指定要创建同义词的对象名称。此时，不会检查该对象是否存在或有效，只有在使用该同义词时才会提示相应错误。

### 7. object@database\_link\_object

该语句用于指定要创建同义词的对象，由dblink连接而来。目前object支持通过dblink连接表与视图两种类型的对象。此时，不会检查该对象是否存在或有效，只有在使用该同义词时才会提示相应错误。

示例（单机、共享集群部署）

```sql
-- 创建本用户对象的公共同义词
CREATE PUBLIC SYNONYM sy_area1 FOR area;
 
-- 创建其他用户对象的私有同义词，即使area表不存在，也可以创建成功
CREATE SYNONYM sy_area2 FOR sales.area;
```

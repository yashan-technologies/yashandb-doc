通用描述
----

CREATE MATERIALIZED VIEW语句用于创建一个物化视图，该物化视图包含了子查询的结果。区别于普通视图，物化视图会占用物理存储空间。

物化视图只能基于单机部署或共享集群/分布式集群部署中的HEAP表创建，存算一体分布式集群部署中无物化视图相关功能。

创建物化视图需具备相应权限：

- 物化视图创建者（即执行CREATE MATERIALIZED VIEW语句的用户）：应具备创建物化视图的权限。
- 物化视图所有者（即物化视图所属schema对应用户）：应具备创建表的权限和访问（SELECT或READ）物化视图所有基表的权限。
- 创建者与所有者为同一用户时，需同时具备上述权限。

语句定义
----

**create materialized view::=**

```ebnf
= CREATE MATERIALIZED VIEW [SCHEMA "."] materialized_view_name 
["("(column_name {"," column_name})")"]
[TABLESPACE tablespace_name] 
[BUILD (IMMEDIATE | DEFERRED)] 
[create_mv_refresh_clause] 
[query_rewrite_clause] 
AS subquery.
```

**create_mv_refresh_clause::=**

```ebnf
= REFRESH ([COMPLETE | FORCE] 
| [ON DEMAND | ON COMMIT] 
| [((START WITH date) | NEXT date)]){" "([COMPLETE | FORCE] 
| [ON DEMAND | ON COMMIT] 
| [((START WITH date) | NEXT date)])}
| (NEVER REFRESH).
```

**query_rewrite_clause**

```ebnf
= (ENABLE | DISABLE) QUERY REWRITE.
```

### materialized\_view\_name

该语句用于指定创建的物化视图名称，不可省略，且需符合YashanDB的[对象命名规范](../基本SQL元素/标识符)。

如需使用`SCHEMA.materialized_view_name`，则要求当前用户具备在指定schema下创建物化视图的权限以及要求目标schema用户具备创建表的权限。

#### column\_name

该语句用于指定物化视图列字段的别名，可以省略，不指定数据类型，且需符合YashanDB的[对象命名规范](../基本SQL元素/标识符)。

### TABLESPACE

该语句用于指定所创建的物化视图对应的持久化数据存储在哪个表空间上。

示例（HEAP表）

```sql
CREATE MATERIALIZED VIEW mv1 TABLESPACE USERS AS SELECT * FROM area;
```

### BUILD (IMMEDIATE|DEFERRED)

该语句用于指定物化视图是否在创建时同步刷新数据，可省略，省略则默认为BUILD IMMEDIATE。

- IMMEDIATE表示物化视图创建时刷新数据，该选项会导致物化视图创建过程耗费时间较长。

- DEFERRED表示物化视图创建时仅创建基础定义，不刷新数据，后续依据其他方式进行刷新。

示例（HEAP表）

```sql
CREATE MATERIALIZED VIEW mv2 BUILD IMMEDIATE AS SELECT * FROM area;
```

### create_mv_refresh_clause

该语句用于指定物化视图刷新相关的属性，包括刷新类型、刷新模式、定时刷新等，多项间使用空格进行分隔。

#### COMPLETE|FORCE

该语句用于指定物化视图的刷新类型，指定COMPLETE或FORCE其中一项或直接省略，不论指定为何值目前均以全量刷新生效。

#### ON DEMAND|ON COMMIT

该语句用于指定物化视图的刷新模式，包括：

- ON DEMAND: 手动刷新，即[DBMS_MVIEW](../../PL参考手册/内置高级包/DBMS_MVIEW)高级包刷新
- ON COMMIT: 自动刷新，事务提交时刷新相关联的物化视图

刷新模式最多能够指定一项，省略则默认为ON DEMAND。

当物化视图的基表包含DBLINK远端表时，不支持配置刷新模式为ON COMMIT。

#### START WITH date|NEXT date

该语句用于指定物化视图的定时刷新选项，包括：

- START WITH date：指定第一次刷新时间
- NEXT date：指定刷新时间间隔

定时刷新选项可以指定一项或指定两项，也可省略表示不进行定时刷新。

下一次刷新时间计算后必须是未来某个时间。

#### NEVER REFRESH

该语句用于指定不刷新。

指定后保护物化视图不被任何自动刷新、高级包机制刷新，并忽略任何发出的刷新语句、高级包执行。

示例（HEAP表）
```sql
CREATE MATERIALIZED VIEW mv_refresh(mya,myb) BUILD IMMEDIATE
REFRESH
COMPLETE
ON DEMAND
NEXT SYSDATE + 10/(2460)
AS SELECT area_no,area_name FROM area;
```

### query_rewrite_clause

该语句用于指定该物化视图是否可以用于查询重写功能，可省略，省略则默认不可用于查询重写。

可通过查询`QUERY_REWRITE_ENABLED`参数查看当前物化视图查询重写功能的具体情况。

查询重写功能包含如下限制：

- 物化视图不允许包含可变的内置函数（例如时间相关函数）、自定义函数和自定义高级包等。
- 物化视图不允许包含表函数。
- 物化视图不允许包含伪列。
- 物化视图的基表不允许为dblink远端表。
- 当物化视图基于的表定义修改，导致物化视图失效，此时该物化视图不可进行查询重写。表定义修改包括：
  - 选中的基表列字段数据类型更改
  - 选中的基表列字段删除
  - 选中的基表列字段名称更改

#### (ENABLE|DISABLE) QUERY REWRITE

具体含义如下：

- ENABLE QUERY REWRITE：允许物化视图用于查询重写。

- DISABLE QUERY REWRITE：不允许物化视图用于查询重写。

### subquery

该语句用户指定创建物化视图的子查询语句，子查询规则同[SELECT](SELECT)语句中描述。 其中，子查询的列项不能指定为某个序列号或UDT列。

如创建时指定了物化视图的列字段，则子查询中的列字段数量需与物化视图列字段数量相同，否则返回错误。

如在子查询中包含了对某个表的`SELECT *`操作时：

*   创建的物化视图将该表的所有列字段作为自己的列字段。
*   如该表增加了新的列字段，则本物化视图不会体现该变化，仍只包含原来的列字段项。
*   如该表删除了某个列字段，则本物化视图对象失效，无法使用，直到该列字段被重新增加。
*   如该表更名了某个列字段，则本物化视图对象失效，无法使用，直到该列字段被改回原来的名称。

示例（HEAP表）

```sql
CREATE MATERIALIZED VIEW mv_subqu AS SELECT * FROM area;

CREATE MATERIALIZED VIEW mv_subquery(mya,myb) AS SELECT b.branch_name, a.area_name
FROM branches b, area a
WHERE a.area_no=b.area_no AND b.branch_no LIKE '01%';

--列字段数量不匹配时返回错误
CREATE MATERIALIZED VIEW mv_colmismatch(c1,c2,c3) AS SELECT area_no,area_name FROM area;
[1:26]YAS-04304 the count of column is mismatched
```

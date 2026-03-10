通用描述
----

ALTER MATERIALIZED VIEW语句用于更改一个已存在的物化视图的相关属性。

存算一体分布式集群部署中无物化视图相关功能。

语句定义
----

**alter materialized view::=**

```ebnf+diagram
syntax::= ALTER MATERIALIZED VIEW [schema"."] materialized_view_name (alter_mv_refresh_properties |  alter_query_rewrite_clause ) 
```

**alter_mv_refresh_properties::=**

```ebnf+diagram
syntax::= REFRESH ([COMPLETE | FORCE] 
| [ON DEMAND | ON COMMIT] 
| [((START WITH date) | NEXT date)]){" " ([COMPLETE | FORCE] 
| [ON DEMAND | ON COMMIT] 
| [((START WITH date) | NEXT date)])}
| (NEVER REFRESH)
```

**alter_query_rewrite_clause**

```ebnf+diagram
syntax::= (ENABLE | DISABLE) QUERY REWRITE
```

### 1. materialized\_view\_name

该语句用于指定已存在的物化视图的名称。

### 2. alter\_mv\_refresh\_properties

该语句用于更改物化视图刷新相关的属性，包括刷新类型、刷新模式、定时刷新等，多项间使用空格进行分隔。

#### 2.1. COMPLETE|FORCE

该语句用于指定物化视图的刷新类型，包括：

- COMPLETE：全量刷新
- FORCE：若条件满足，优先使用快速刷新；若快速刷新不可用，则使用全量刷新

刷新类型最多能够指定一项。

#### 2.2. ON DEMAND|ON COMMIT

该语句用于指定物化视图的刷新模式，包括：

- ON DEMAND: 手动刷新，即[DBMS_MVIEW](../内置高级包/DBMS_MVIEW)高级包刷新
- ON COMMIT: 自动刷新，事务提交时刷新相关联的物化视图

刷新模式最多能够指定一项。

#### 2.3. START WITH date|NEXT date

该语句用于指定物化视图的定时刷新选项，包括：

- START WITH date：指定第一次刷新时间
- NEXT date：指定刷新时间间隔

定时刷新选项可以指定一项或指定两项。

下一次刷新时间计算后必须是未来某个时间。

#### 2.4. NEVER REFRESH

该语句用于指定不刷新。

指定后保护物化视图不被任何自动刷新、高级包机制刷新，并忽略任何发出的刷新语句、高级包执行。

示例（HEAP表）

```sql
ALTER MATERIALIZED VIEW mv_refresh 
REFRESH 
COMPLETE 
ON DEMAND 
NEXT SYSDATE + 1/(24*60);
```

### 3. alter\_query\_rewrite\_clause

该语句用于更改该物化视图的查询重写选项。

可通过查询`QUERY_REWRITE_ENABLED`参数查看当前物化视图查询重写功能的具体情况。

查询重写功能包含如下限制：

- 物化视图不允许包含可变的内置函数（例如时间相关函数）、自定义函数和自定义高级包等。
- 物化视图不允许包含表函数。
- 物化视图不允许包含伪列。
- 当物化视图基于的表定义修改，导致物化视图失效，此时该物化视图不可进行查询重写。
  - 选中的基表列字段数据类型更改
  - 选中的基表列字段删除
  - 选中的基表列字段名称更改

#### 3.1. (ENABLE|DISABLE) QUERY REWRITE

具体含义如下：

- ENABLE QUERY REWRITE: 允许物化视图用于查询重写
- DISABLE QUERY REWRITE: 不允许物化视图用于查询重写

示例（HEAP表）
```sql
ALTER MATERIALIZED VIEW mv_refresh ENABLE QUERY REWRITE;
```

通用描述
----

DROP ACCESS CONSTRAINT用于删除一个AC对象，此时该AC所占的数据空间也全部被释放。

语句定义
----

**drop access constraint::=**

```ebnf+diagram
syntax::= DROP ACCESS CONSTRAINT ac_name
```

### 1. ac\_name

指定要删除的AC的名称。



示例（LSC表）

```sql
DROP ACCESS CONSTRAINT ac_area;
```


通用描述
----

CREATE VIEW用于创建一个视图对象。创建的视图可以被SELECT，满足一定条件时可作为INSERT/UPDATE/DELETE的对象。

本语句可能包含对某个表的DML操作，则需要拥有对该表的相应DML权限才能成功创建视图，且同时遵循相应DML的一切约束。

语句定义
----

**create view::=**

```ebnf+diagram
syntax::= CREATE [OR REPLACE] [FORCE|NO FORCE] [EDITIONABLE|NONEDITIONABLE] VIEW [schema "."] view_name ["(" (alias) {","(alias)} ")"] AS subquery [subquery_restriction_clause]
```

**subquery_restriction_clause::=**

```ebnf+diagram
syntax::= WITH (READ ONLY | CHECK OPTION) [CONSTRAINT constraint]
```

### 1. OR REPLACE

该语句用于指定当要创建的视图已经存在时，将其进行重建。

### 2. FORCE|NO FORCE

指定FORCE表示强行创建视图。默认为NO FORCE，表示条件不满足时返回报错，而不是强行创建视图。

当视图依赖的子查询内对象不存在，或者视图的owner对子查询内依赖的对象无访问权限时，指定FORCE可以强行创建视图，但实际使用该视图可能会报错。

使用FORCE的好处是，在所依赖对象被创建，并且视图owner有依赖对象的访问权限后，无需重新创建即可以成功查询该视图。

示例

```sql
CREATE FORCE VIEW v_table_not_exsits AS SELECT * FROM table_not_exsits;
```

### 3. EDITIONABLE | NONEDITIONABLE

用于语法兼容，无实际含义。

### 4. view\_name

该语句用于指定要创建的视图的名称，不可省略，且需符合YashanDB的[对象命名规范](../../全部手册/开发手册/SQL参考手册/基本SQL元素/标识符)。

### 5. alias

该语句用于指定视图列字段的别名，可省略，且需符合YashanDB的[对象命名规范](../../全部手册/开发手册/SQL参考手册/基本SQL元素/标识符)，不可用于更改列字段的数据类型。

### 6. AS subquery

该语句用于指定创建视图的子查询语句，子查询规则同[SELECT](./SELECT)语句中描述。其中，子查询的列项不能指定为某个序列号。

如在子查询中包含了对某个表的SELECT \*操作时：

*   创建的视图将该表的所有列字段作为自己的列字段。
*   如该表增加了新的列字段，则本视图不会体现该变化，仍只包含原来的列字段项。

示例

```sql
CREATE VIEW v_area AS SELECT * FROM area;
```

**视图失效**

当视图用到的子对象发生以下变化时，视图失效，其status变为invalid。

*   基表删除了某个列字段，则本视图对象失效，重新查询视图时：
    *   若删除非视图依赖的列，查询视图触发视图重新变成有效。
    *   若删除视图依赖的列，查询视图，视图仍失效，视图的列信息变成unknown。此时需重新添加删除的列，再次查询视图。
*   基表更名了某个列字段，则本视图对象失效，重新查询视图时：
    *   若更名非视图依赖的列，查询视图触发视图重新变成有效。
    *   若更名视图依赖的列，查询视图，视图仍失效，视图的列信息变成unknown。此时需重新将该列改回原来的名称，再次查询视图。
*   基表修改了某个列字段（数据类型，属性），则本视图对象失效，重新查询视图时：
    *   若修改不影响视图，视图重新编译成功，查询视图触发视图变成有效。
    *   若修改会影响视图，导致编译失败，视图仍失效，视图的列信息变成unknown。此时需将该列还原，再次查询视图。
*   删除或更名视图依赖的对象，也会导致视图失效。

**视图元数据**

当视图依赖的对象发生变更（如上所述），会触发视图的元数据刷新。

当视图依赖的对象是同义词或dblink时，连接对象的变更无法失效视图，也就无法刷新视图的元数据。

老版本升级到当前版本之后，视图是valid状态，但是视图的元数据可能不正确（基表发生过结构变更），视图的元数据刷新不了。此时需重建视图或对基表执行DDL，触发视图刷新元数据。

若视图失效，备库查询会报错，需要在主库上重新编译视图刷新状态从而在备库查询。

### 7. WITH READ ONLY

指定该语句表示所创建的视图只读，即不能更改视图中的数据，YashanDB默认即为此模式。

示例

```sql
CREATE VIEW v_area_readonly AS SELECT * FROM area WITH READ ONLY;
```

### 8. WITH CHECK OPTION

指定该子句表示所创建的视图在进行insert与update操作时会进行额外检查，当待插入的数据或更新后的数据不满足视图建立时基表的原始过滤条件（即若更新或插入成功，更新或插入的数据不会在视图中出现）时将会报错。该校验仅在视图基表有且仅有一个时生效，若基表有多个，此时效用等同于READ ONLY子句。

示例（HEAP表）

```sql
CREATE VIEW v_area_check_option AS SELECT * FROM area WHERE area_no > 5 WITH CHECK OPTION;

INSERT INTO v_area_check_option VALUES('02', 'WestChina', 'Chengdu');

YAS-02755 view WITH CHECK OPTION where-clause violation
```

### 9. CONSTRAINT constraint

该语句用于语法兼容。

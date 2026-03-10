通用描述
----

TRANSPORT语句用于在表空间迁移后迁移其相关对象。TRANSPORT对象时只需创建定义，无需创建存储对象，后续需执行ALTER相关对象的RECALAIM子句为其指定存储对象的物理位置。

> **Warn**:
> 
> 存算一体分布式集群部署中进行节点/节点组扩缩容后，数据库内部会自动生成迁移对象相关语句，**不建议用户手动执行TRANSPORT语句**，若不避免可能会导致系统崩溃或其他不可预知的结果。

语句定义
----

**transport object::=**

```ebnf+diagram
syntax::= TRANSPORT 
[transport_table_clause] [transport_index_clause] [transport_ac_clause] [transport_view_clause]
[transport_comment_clause] [transport_alter_table_clause] [transport_user_clause] [transport_role_clause]
[transport_profile_clause] [transport_grant_clause]
[transport_tablespace_clause] [transport_alter_tablespace_clause]
```

**transport_table_clause**

```ebnf+diagram
syntax::= [ SHARED | DUPLICATE] TABLE [schema "."] table_name [OBJNO object_id VERSION version GLOBAL_CHUNKS chunk DSID ds_id DSVERSION ds_version] create_table_clause
```
**transport_index_clause**

```ebnf+diagram
syntax::= [UNIQUE|COLUMNAR|RTREE] INDEX [schema"."] index_name create_index_clause
```

**transport_ac_clause**

```ebnf+diagram
syntax::= ACCESS CONSTRAINT [OBJNO object_id ] [schema "."] ac_name  create_access_constraint_clause
```

**transport_view_clause**

```ebnf+diagram
syntax::= VIEW [OBJNO object_id VERSION version] [schema "."] view_name  create_view_clause
```

**transport_comment_clause**

```ebnf+diagram
syntax::= COMMENT ON  comment_clause
```

**transport_alter_table_clause**

```ebnf+diagram
syntax::= ALTER TABLE [schema "."] table_name alter_table_clause
```

**transport_user_clause**

```ebnf+diagram
syntax::= USER [OBJNO object_id VERSION version USERNO user_no EXPTIME exp_date] user_name create_user_clause
```

**transport_role_clause**

```ebnf+diagram
syntax::= ROLE [OBJNO object_id VERSION version USERNO user_no] role_name create_role_clause
```

**transport_profile_clause**

```ebnf+diagram
syntax::= PROFILE [OBJID object_id VERSION version PROFILEID profile_id] create_profile_clause
```

**transport_grant_clause**

```ebnf+diagram
syntax::= GRANT GRANTOR grantor_name grant_clause
```

**transport_tablespace_clause**

```ebnf+diagram
syntax::= create_tablespace_clause
```

**transport_alter_tablespace_clause**

```ebnf+diagram
syntax::= alter_tablespace_clause
```

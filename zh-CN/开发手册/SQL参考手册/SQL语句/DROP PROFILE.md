通用描述
----

DROP PROFILE用于删除profile。

执行本语句需注意如下事项：

- 用户必须拥有DROP PROFILE权限才能删除profile。
- 系统默认的profile（名称为DEFAULT）不能被删除。
- 对于已与用户关联的profile，必须使用CASCADE关键字才能删除，对应用户将会自动关联默认PROFILE。

语句定义
----

**drop profile::=**

```ebnf
= DROP PROFILE profile_name [CASCADE].
```

### profile_name

已存在的一个profile的名称。

### CASCADE

指定CASCADE表示删除profile的同时，也删除其与用户的关联关系。

示例

```sql
DROP PROFILE prof_pswd1;

DROP PROFILE prof_pswd2 CASCADE;
```

示例（单机/共享集群/分布式集群部署）

```sql
DROP PROFILE prof_res;

DROP PROFILE prof_ip CASCADE;
```

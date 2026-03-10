通用描述
----

DROP AUDIT POLICY用于删除一个审计策略。

只有拥有AUDIT_ADMIN审计管理员角色，或者拥有AUDIT SYSTEM系统权限的用户，才能删除一个审计策略。

对于已被使能的审计策略，不允许直接删除，而应该先执行[NOAUDIT POLICY](./NOAUDIT POLICY)之后再进行删除操作。

语句定义
----

**drop\_audit\_policy::=**

```ebnf+diagram
syntax::= DROP AUDIT POLICY policy_name
```

### 1. policy\_name

将要删除的审计策略的名称。

示例

```sql
DROP AUDIT POLICY up1;
DROP AUDIT POLICY up2;
DROP AUDIT POLICY up3;
DROP AUDIT POLICY up4;
DROP AUDIT POLICY audit_role_a;
```

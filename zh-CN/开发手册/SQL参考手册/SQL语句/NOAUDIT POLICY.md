通用描述
----

NOAUDIT POLICY对审计策略取消使能，取消审计策略对应的审计项的审计。

只有拥有AUDIT_ADMIN审计管理员角色，或者拥有AUDIT SYSTEM系统权限的用户，才能对一个审计策略取消使能。

语句定义
----

**noaudit\_policy::=**

```ebnf
= NOAUDIT POLICY policy_name [(BY user_name {',' user_name})] [WHENEVER [ NOT] SUCCESSFUL].
```

### policy\_name

要取消使能的审计策略的名称。

### BY

指定要取消使能的操作用户，该用户必须在审计策略使能时指定的用户列表中。

不指定本语句时，在该审计策略是针对所有用户或者该审计策略是except的情况下才能取消使能 。

### WHENEVER [NOT] SUCCESSFUL

取消审计策略在数据库操作执行成功|失败时进行审计记录。

不指定本语句时，表示取消审计策略在数据库操作执行成功或失败的审计。

示例

```sql
-- 取消用户sales对应的审计策略up1的审计
NOAUDIT POLICY up1 BY sales;

-- 取消用户sales对应的审计策略up2在数据库操作执行失败的审计
NOAUDIT POLICY up2 BY sales WHENEVER NOT SUCCESSFUL;
```

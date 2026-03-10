通用描述
----

AUDIT POLICY对已创建的审计策略进行使能，只有使能的审计策略才会在数据库操作符合审计策略的审计项时触发审计。

只有拥有AUDIT_ADMIN审计管理员角色，或者拥有AUDIT SYSTEM系统权限的用户，才能执行某个审计策略的使能操作。

按照AUDIT POLICY定义规则使能后的审计策略，在配置参数UNIFIED_AUDITING为true的情况下，系统将对数据库的操作进行审计，用户可通过UNIFIED_AUDIT_TRAIL视图查看相应审计记录。

语句定义
----

**audit\_policy::=**

```ebnf+diagram
syntax::= AUDIT POLICY policy_name [((BY | EXCEPT) (user_name) {"," (user_name)})] [WHENEVER [NOT] SUCCESSFUL]
```

### 1. policy\_name

要使能的审计策略名称。

### 2. BY|EXCEPT

指定要审计的操作用户或者排除不需要审计的操作用户，同时指定或排除多个用户以`,`分隔。本语句可省略，则默认审计所有的操作用户。

### 3. WHENEVER [NOT] SUCCESSFUL

指定在数据库操作执行成功|失败时才进行审计记录。不使用本语句则表示针对满足审计项的数据操作，无论其执行成功或失败都将被审计。

示例

```sql
-- 使能审计策略，策略对应用户sales，在策略对应的审计项执行成功时进行审计
AUDIT POLICY up1 BY sales WHENEVER SUCCESSFUL;

-- 使能审计策略，排除用户sales,在策略对应的审计项执行成功或失败都进行审计
AUDIT POLICY up2 EXCEPT sales;
```

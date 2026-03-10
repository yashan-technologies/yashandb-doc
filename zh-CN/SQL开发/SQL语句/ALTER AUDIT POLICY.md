通用描述
----

ALTER AUDIT POLICY用于修改一个审计策略，对审计项目、审计执行条件、审计条件执行频率等进行增删操作。

只有拥有AUDIT_ADMIN审计管理员角色，或者拥有AUDIT SYSTEM系统权限的用户，才能修改一个审计策略。

对审计策略的修改立即生效，系统对数据库操作的审计项目相应发生改变。

语句定义
----

**alter\_audit\_policy::=**

```ebnf+diagram
syntax::= ALTER AUDIT POLICY policy_name (ADD [privilege_audit_clause] [action_audit_clause] [role_audit_clause] [toplevel_clause] | DROP [privilege_audit_clause] [action_audit_clause] [role_audit_clause] [toplevel_clause] | CONDITION condtion_clause)
```
[privilege_audit_clause](./CREATE AUDIT POLICY)  
[action_audit_clause](./CREATE AUDIT POLICY)  
[role_audit_clause](./CREATE AUDIT POLICY)  
[toplevel_clause](./CREATE AUDIT POLICY)  

**condition\_clause::=**

```ebnf+diagram
syntax::= DROP | "'" audit_condition "'" EVALUATE PER (STATEMENT  | SESSION | INSTANCE)
```

### 1. policy\_name

要修改的审计策略的名称。

### 2. ADD

为审计策略增加审计项目，审计项目的语法同[CREATE AUDIT POLICY](./CREATE AUDIT POLICY)中定义。

### 3. DROP

为审计策略删除审计项目，审计项目的语法同[CREATE AUDIT POLICY](./CREATE AUDIT POLICY)中定义。

审计策略应该最少包含一个审计项目，违反此规则的DROP操作将会失败。

### 4. CONDITION

为审计策略删除或增加执行判断条件。

**DROP**

删除在审计策略上定义的执行判断条件。

**audit_condition**

在审计策略上定义一个执行判断条件，语法同[CREATE AUDIT POLICY](./CREATE AUDIT POLICY)中定义。

示例

```sql
-- 修改审计策略，增加对select any table权限的审计
ALTER AUDIT POLICY up2 ADD PRIVILEGES SELECT ANY TABLE;

-- 修改审计策略，增加对create table类型语句审计，删除对表area的delete、insert操作、对表branches所有操作的审计，删除审计条件
ALTER AUDIT POLICY up3
  ADD ACTIONS CREATE TABLE
  DROP ACTIONS DELETE ON sales.area, INSERT ON sales.area, ALL ON sales.branches
  CONDITION DROP;

-- 修改审计策略，删除对不审计内部语句的限制
ALTER AUDIT POLICY up4
  DROP ONLY TOPLEVEL;
```

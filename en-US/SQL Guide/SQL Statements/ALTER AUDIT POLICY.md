General Description
----

ALTER AUDIT POLICY is used to modify an audit policy, adding or deleting audit items, audit execution conditions, audit condition execution frequencies, etc.

Only users with the AUDIT_ADMIN audit administrator role or users with the AUDIT SYSTEM system privilege can modify an audit policy.

Modifications to the audit policy take effect immediately, and the audit items for database operations will change accordingly.

Statement Definition
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

The name of the audit policy to be modified.

### 2. ADD

Adds an audit item to the audit policy. The syntax for the audit item is defined the same as in [CREATE AUDIT POLICY](CREATE AUDIT POLICY).

### 3. DROP

Removes an audit item from the audit policy. The syntax for the audit item is defined the same as in [CREATE AUDIT POLICY](CREATE AUDIT POLICY).

An audit policy must contain at least one audit item; a DROP operation that violates this rule will fail.

### 4. CONDITION

Adds or removes execution judgment conditions for the audit policy.

**DROP**

Removes the execution judgment conditions defined on the audit policy.

**audit_condition**

Defines an execution judgment condition on the audit policy, with syntax as defined in [CREATE AUDIT POLICY](CREATE AUDIT POLICY).

***Example***

```sql
-- Modify the audit policy to add auditing for select any table privilege
ALTER AUDIT POLICY up2 ADD PRIVILEGES SELECT ANY TABLE;

-- Modify the audit policy to add auditing for create table type statements, remove auditing for delete and insert operations on table area, remove auditing for all operations on table branches, and remove audit conditions
ALTER AUDIT POLICY up3
  ADD ACTIONS CREATE TABLE
  DROP ACTIONS DELETE ON sales.area, INSERT ON sales.area, ALL ON sales.branches
  CONDITION DROP;

-- Modify the audit policy to remove the restriction on not auditing internal statements
ALTER AUDIT POLICY up4
  DROP ONLY TOPLEVEL;
```

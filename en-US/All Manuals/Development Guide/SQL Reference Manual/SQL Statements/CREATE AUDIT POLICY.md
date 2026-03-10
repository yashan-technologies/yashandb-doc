General Description
----

CREATE AUDIT POLICY is used to create an audit policy that audits database operations by specifying different audit items.

Only users who have the AUDIT_ADMIN audit administrator role or who have the AUDIT SYSTEM system privilege can create an audit policy.

A successfully created audit policy must also meet the following two prerequisites for the system to start auditing operations:

- The configuration parameter UNIFIED_AUDITING must be set to true, meaning the audit switch is turned on.
- The audit policy must be enabled using the [AUDIT POLICY](AUDIT POLICY) statement.

After the audit policy is successfully created, its definition can be viewed through the AUDIT_UNIFIED_POLICIES view.

YashanDB provides users with the following aspects of audit management:

- [System privilege auditing](#privilegeauditclause)
- [Auditing operations for system behavior and object behavior](#actionauditclause)
- [Role auditing](#roleauditclause)

For a more specific description of audit management, please refer to [Security Audit](../../../Product Security/Security Audit/00Security Audit).

Statement Definition
----

**create audit policy::=**

```ebnf+diagram
syntax::= CREATE AUDIT POLICY policy_name [privilege_audit_clause] [action_audit_clause] [role_audit_clause] [when_clause] [toplevel_clause]
```

**At least one of the following clause parameters must be selected.**

**[privilege\_audit\_clause](#privilegeauditclause)::=**

```ebnf+diagram
syntax::= PRIVILEGES (system_privileges_clause) {"," (system_privileges_clause)}
```

**[action\_audit\_clause](#actionauditclause)::=**

```ebnf+diagram
syntax::= ACTIONS (system_action_clause|object_action_clause) {"," (system_action_clause|object_action_clause)}
```

**[system_action_clause](#systemactionclause)::=**

```ebnf+diagram
syntax::= system_action|ALL
```

**[object_action_clause](#objectactionclause)::=**

```ebnf+diagram
syntax::= (object_action|ALL) ON [schema "."] object_name
```

**[role\_audit\_clause](#roleauditclause)::=**

```ebnf+diagram
syntax::= ROLES (role_name) {"," (role_name)}
```

**[when\_clause](#whenclause)::=**

```ebnf+diagram
syntax::= WHEN "'" audit_condition "'" EVALUATE PER (STATEMENT|SESSION|INSTANCE)
```

**[toplevel\_clause](#toplevelclause)::=**

```ebnf+diagram
syntax::= ONLY TOPLEVEL
```

### 1. policy\_name

The name of the audit policy. The audit policy name must be unique, cannot be omitted, cannot include a schema name, and must conform to YashanDB's [object naming conventions](../Basic SQL Elements/Identifiers).

<span id="privilegeauditclause" name="privilegeauditclause" class="yaslink"></span>

### 2. privilege\_audit\_clause

This statement is used to define the system privileges included in the audit policy, allowing multiple system privilege audit items, separated by `,`.

**system_privileges_clause**

Specifies specific system privilege names. For details on specific privilege items, please refer to [System Privileges](../../../Product Security/Data Access Control/Privilege and Role Management/System Privileges).

When the audit policy for a specified system privilege is enabled, all operations that use that privilege will be audited.

***Example***

```sql
-- Create an audit policy to audit statements involving select any table, delete any table privilege
CREATE AUDIT POLICY up1
PRIVILEGES SELECT ANY TABLE, DELETE ANY TABLE;
```

<span id="actionauditclause" name="actionauditclause" class="yaslink"></span>

### 3. action\_audit\_clause

This statement is used to define the system behavior or object behavior audit items included in the audit policy, allowing multiple behavior audit items, separated by `,`. The audit items can be categorized into the following two types:

- Global audit items.
- Audit items for specific objects.

<span id="systemactionclause" name="systemactionclause" class="yaslink"></span>

#### 3.1. system\_action\_clause

Specifies global audit items.

##### 3.1.1. system\_action

Global audit items, and specific items can be viewed through the AUDITABLE_SYSTEM_ACTIONS view.

##### 3.1.2. ALL

When the global audit item is specified as ALL, it means that all items listed in AUDITABLE_SYSTEM_ACTIONS will be included in the audit policy.

<span id="objectactionclause" name="objectactionclause" class="yaslink"></span>

#### 3.2. object\_action\_clause

Specifies audit items for specific objects.

##### 3.2.1. object\_action

Audit items for objects, and specific items can be viewed through the AUDITABLE_OBJECT_ACTIONS view.

##### 3.2.2. schema

Schema name, which can be omitted to use the currently logged-in user's schema.

##### 3.2.3. object\_name

Object name. YashanDB supports auditing operations on all objects, such as tables and views.

***Example***

```sql
-- Create an audit policy to audit DROP TABLE statements, DELETE, INSERT, UPDATE operations on the area table, and all operations on branches
CREATE AUDIT POLICY up2
  ACTIONS DROP TABLE,
    DELETE ON sales.area,
    INSERT ON sales.area,
    UPDATE ON sales.area,
    ALL ON sales.branches;
```

<span id="roleauditclause" name="roleauditclause" class="yaslink"></span>

### 4. role\_audit\_clause

This statement is used to define audit items for roles included in the audit policy, allowing multiple role audit items, separated by `,`.

**role_name**

Role name, which can only be a NORMAL ROLE. Specific items can be viewed in the DBA_ROLES view.

When the audit policy for a specified role is enabled, all system privileges directly granted to that role will be audited, meaning all operations that use these privileges will be audited.

***Example***

```sql
-- Create a role
CREATE ROLE role_a;
-- Grant system privilege to the role
GRANT CREATE SESSION TO role_a;
-- Create the role audit policy. Once enabled, this policy will take effect, and sessions connecting within its scope will be audited
CREATE AUDIT POLICY audit_role_a ROLES role_a;
```

<span id="whenclause" name="whenclause" class="yaslink"></span>

### 5. when\_clause

This statement defines the condition for executing the audit policy. If the condition evaluates to true, the audit policy will be executed; otherwise, it will not.

#### 5.1. audit\_condition

Condition expression for simple condition evaluation, resulting in either true or false. The content can include:

* Mathematical functions: BITAND, CEIL, FLOOR, POWER
* String functions: CONCAT, LOWER, UPPER
* Length calculation functions: INSTR, LENGTH
* Environment information related functions: SYS_CONTEXT
* Comparison operators: =, !=, <>, \<, >, \<=, >=
* Logical operators: AND, OR
* [NOT] BETWEEN
* [NOT] IN

Using SYS_CONTEXT in the audit condition expression is not applicable for ISC Distributed Cluster Deployment.

#### 5.2. EVALUATE PER (STATEMENT|SESSION|INSTANCE)

Indicates the frequency of condition evaluation, corresponding to statement level, SESSION level, or instance level (the condition is evaluated only once during the lifecycle of an instance).

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Create an audit policy to audit SELECT queries on the area table when the given condition is satisfied
CREATE AUDIT POLICY up_SYS_CONTEXT
  ACTIONS SELECT ON sales.area
  WHEN 'SYS_CONTEXT(''USERENV'', ''OS_USER'') = ''SALES'''
  EVALUATE PER SESSION;
```

***Example***

```sql
-- Create an audit policy to audit SELECT queries on the area table when the given condition is satisfied
CREATE AUDIT POLICY up3
  ACTIONS SELECT ON sales.area
  WHEN '2>1'
  EVALUATE PER SESSION;
```

<span id="toplevelclause" name="toplevelclause" class="yaslink"></span>

### 6. toplevel\_clause

This statement defines whether to audit SQL statements within anonymous blocks, stored procedures, functions, etc. Specifying ONLY TOPLEVEL means that internal statements will not be audited.

***Example***

```sql
-- Create an audit policy to audit statements involving SELECT ANY TABLE privileges but not audit internal statements of anonymous blocks, stored procedures, or functions.
CREATE AUDIT POLICY up4
  PRIVILEGES SELECT ANY TABLE
  ONLY TOPLEVEL;
```

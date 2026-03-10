## General Description

GRANT is used to grant privileges to a user. The grantable content includes [system privilege SYSTEM PRIVILEGE](../../../All Manuals/Reference Manual of mysql Mode/产品安全/特权管理/系统特权), [schema privilege SCHEMA PRIVILEGE](../../../All Manuals/Reference Manual of mysql Mode/产品安全/特权管理/模式特权), and [object privilege OBJECT PRIVILEGE](../../../All Manuals/Reference Manual of mysql Mode/产品安全/特权管理/对象特权).

The privilege granted to the user takes effect immediately.

## Statement Definition

**grant::=**

```ebnf+diagram
syntax::= grant_system_privilege|grant_schema_privilege|grant_object_privilege
```

**[grant\_system\_privilege](#grant_system_privilege)::=**

```ebnf+diagram
syntax::= GRANT ((system_privilege)) {"," (system_privilege)} ON "*.*" TO user_name [WITH GRANT OPTION]
```

**[grant\_schema\_privileg](#grant_schema_privilege)e::=**

```ebnf+diagram
syntax::= GRANT ((schema_privilege)) {"," (schema_privilege)} ON [schema "."]"*"  TO user_name [WITH GRANT OPTION]
```

**[grant\_object\_privilege](#grant_object_privilege)::=**

```ebnf+diagram
syntax::= GRANT ((object_privilege)) {"," (object_privilege)} ON [TABLE] [schema "."] table_name TO user_name [WITH GRANT OPTION]
```

<span id="grant_system_privilege" name="grant_system_privilege" class="yaslink"></span>

### grant\_system\_privilege

This statement is used to grant system privileges to a user.

#### system\_privilege

The name of the system privileges to be granted, separated by commas. Please refer to the list of system privileges in [System Privileges](../../../All Manuals/Reference Manual of mysql Mode/产品安全/特权管理/系统特权).

#### user\_name

The name of the user being granted privileges.

#### WITH GRANT OPTION

When granting system privileges to a user, this clause indicates that the user has the management privilege over this privilege, meaning they can grant this system privilege to other users.

***Example*** for Standalone Deployment Heap tables

```sql
-- Grant CREATE USER privilege to user SALES1 under user SALES with WITH GRANT OPTION
GRANT CREATE USER ON *.* TO SALES1 WITH GRANT OPTION;
 
-- At this point, SALES1 can grant the above privilege to other users
conn SALES1/********
GRANT CREATE USER ON *.* TO SALES2;
```

<span id="grant_schema_privilege" name="grant_schema_privilege" class="yaslink"></span>

### grant\_schema\_privilege

This statement is used to grant schema privileges to a user.

Schema privileges do not take effect for the sys schema.

#### schema\_privilege

The name of the schema privileges to be granted, separated by commas. Please refer to the list of schema privileges in [Schema Privileges](../../../All Manuals/Reference Manual of mysql Mode/产品安全/特权管理/模式特权).

#### [schema.]*

The name of the schema, which must be an existing schema.

#### user\_name

The name of the user being granted privileges.

#### WITH GRANT OPTION

When granting schema privileges to a user, this clause indicates that the user has the management privilege over this privilege, meaning they can grant this schema privilege to other users.

***Example*** for Standalone Deployment Heap tables

```sql
-- Grant all privileges on the specified schema to user SALES1 under user SALES
conn SALES/********
GRANT ALL ON sales.* TO SALES1 WITH GRANT OPTION;
```

<span id="grant_object_privilege" name="grant_object_privilege" class="yaslink"></span>

### grant\_object\_privilege

This statement is used to grant object privileges to a user.

#### object\_privilege

The name of the object privileges to be granted, separated by commas. Please refer to the list of object privileges in [OBJECT PRIVILEGE](../../../All Manuals/Reference Manual of mysql Mode/产品安全/特权管理/对象特权).

#### [schema.]table\_name

The name of the table, which must be an existing table.

#### user\_name

The name of the user being granted privileges.

#### WITH GRANT OPTION

When granting object privileges to a user, this clause indicates that the user can grant this object privilege to other users.

***Example*** for Standalone Deployment Heap tables

```sql
-- Grant all privileges on the specified table to user SALES2 under user SALES1
GRANT ALL ON TABLE sales.sales_info TO SALES2;
```

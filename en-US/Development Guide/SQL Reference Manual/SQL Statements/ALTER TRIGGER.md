General Description
----

The ALTER TRIGGER statement is used to change the attributes of an existing trigger or explicitly recompile a trigger.

For triggers in the SYS schema, the ALTER TRIGGER statement must be executed by the SYS user.

For triggers in other schemas, the ALTER TRIGGER statement must be executed by the owning user or a user with the ALTER ANY TRIGGER privilege.

For the meaning and limitations of triggers, please refer to the [Triggers](../../PL Reference Manual/PL Objects/Triggers) section.

Statement Definition
----

**alter_trigger ::=**

```ebnf
= ALTER TRIGGER [schema "."] trigger_name
  (trigger_compile_clause
  | (ENABLE | DISABLE)
  | RENAME TO new_name
  | (EDITIONABLE | NONEDITIONABLE)).
```

### schema

The name of the schema containing the trigger. If omitted, the current logged-in user's schema is assumed.

### trigger\_name

The name of the trigger to be changed or recompiled.

### trigger\_compile\_clause

Specifies the recompilation options. For details, see the description of [compile_clause](../General SQL Syntax/compile_clause).

### ENABLE|DISABLE

Enables or disables the trigger.

### RENAME TO new_name

Modifies the name of the trigger.  

When changing the trigger name, the specified new name must not be empty and must conform to YashanDB's [object naming conventions](../Basic SQL Elements/Identifiers).

### EDITIONABLE | NONEDITIONABLE

Used for syntax compatibility, with no actual meaning.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Explicitly recompile the tri trigger in the sales schema
ALTER TRIGGER sales.tri COMPILE;

-- Disable the tri trigger in the sales schema
ALTER TRIGGER sales.tri DISABLE;

-- Enable the tri trigger in the sales schema
ALTER TRIGGER sales.tri ENABLE;

-- Rename the tri trigger in the sales schema
ALTER TRIGGER sales.tri RENAME TO newTri;
ALTER TRIGGER sales.newTri RENAME TO tri;
```

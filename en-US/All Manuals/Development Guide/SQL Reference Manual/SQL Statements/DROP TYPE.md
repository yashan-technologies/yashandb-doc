General Description
----

The DROP TYPE statement is used to delete an existing [UDT] (..\..\PL Reference Manual\PL Objects\User-Defined Types), and it also removes the type body associated with that type.

Statement Definition
----

**DROP TYPE::=**

```ebnf+diagram
syntax::=  DROP TYPE [IF EXISTS] [ schema "." ]  type_name [FORCE]
```

### 1. IF EXISTS

This clause is used to specify whether to check for the existence of the UDT before attempting to delete it. If omitted, no check is performed, and if the UDT to be deleted does not exist, an error will be raised by the system.

### 2. schema

The name of the schema that contains the UDT. If omitted, the default is the schema of the currently logged-in user.

### 3. type\_name

The name of the UDT to be deleted.

### 4. FORCE

The FORCE option allows the type and its type body to be forcibly deleted even if the type is dependent on other objects.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Delete the udt_object UDT in the current schema, which will also remove the udt_object type body (if it exists).
DROP TYPE udt_object;
```

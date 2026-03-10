General Description
----

The DROP TYPE BODY statement is used to delete an existing type body.

Statement Definition
----

**DROP TYPE BODY::=**

```ebnf+diagram
syntax::=  DROP TYPE BODY [IF EXISTS] [ schema "." ]  type_name
```

### 1. IF EXISTS

This clause specifies that the existence of the type body should be checked before attempting to delete it. If omitted, no check will be performed, and an error will be raised if the type body to be deleted does not exist.

### 2. schema

The name of the schema that contains the type body. If omitted, it defaults to the schema of the currently logged-in user.

### 3. type\_name

The name of the type body to be deleted.

***Example*** for Standalone Deployment and YAC Deployment

```sql
-- Delete the type body udt_object in the current schema, without deleting the existing type udt_object.
DROP TYPE BODY udt_object;
```

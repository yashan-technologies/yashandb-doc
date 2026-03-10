General Description
----

The DROP TRIGGER statement is used to delete an existing [trigger](../../All Manuals/Development Guide/PL Reference Manual/PL Objects/Triggers).

Statement Definition
----

**DROP TRIGGER::=**

```ebnf+diagram
syntax::=  DROP TRIGGER [IF EXISTS] [ schema "." ]  trigger_name
```

### 1. IF EXISTS

This clause is used to specify that the existence of the trigger should be checked before attempting to drop it. If omitted, no check will be performed; thus, if the trigger to be deleted does not exist, the system will issue an error.

### 2. schema

This refers to the name of the schema that contains the trigger. If omitted, it defaults to the current logged-in user's schema.

### 3. trigger\_name

The name of the trigger to be deleted.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Delete the tri trigger in the sales schema
DROP TRIGGER sales.tri;
```

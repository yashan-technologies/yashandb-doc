General Description
----

DROP CONTEXT is used to delete context objects. The user executing this statement must have the drop any context privilege.

Statement Definition
----

**drop context::=**

```ebnf+diagram
syntax::= DROP CONTEXT namespace
```

### 1. namespace

This statement is used to specify the name of the context to be deleted and cannot be omitted.

***Example*** for Standalone Deployment and YAC Deployment

```sql
DROP CONTEXT ctx1;
```

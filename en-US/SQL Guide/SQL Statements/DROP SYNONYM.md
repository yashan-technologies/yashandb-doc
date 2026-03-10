General Description
----

DROP SYNONYM is used to delete a synonym.

In ISC Distributed Cluster Deployment, users cannot execute this statement.

Statement Definition
----

**drop synonym::=**

```ebnf+diagram
syntax::= DROP [PUBLIC] SYNONYM synonym_name
```

### 1. PUBLIC

When deleting a public synonym, this clause cannot be omitted and must be specified.

### 2. synonym\_name

This statement is used to specify the name of the synonym to be deleted.

***Example*** for Standalone Deployment and YAC Deployment

```sql
-- Delete public synonym
DROP PUBLIC SYNONYM sy_area1;
 
-- Delete private synonym under the current user
DROP SYNONYM sy_area2;
```

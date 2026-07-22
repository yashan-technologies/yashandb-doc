General Description
----

DROP SYNONYM is used to delete a synonym.

In ISC Distributed Cluster Deployment, users cannot execute this statement.

Statement Definition
----

**drop synonym::=**

```ebnf
= DROP [PUBLIC] SYNONYM synonym_name.
```

### PUBLIC

When deleting a public synonym, this clause cannot be omitted and must be specified.

### synonym\_name

This statement is used to specify the name of the synonym to be deleted.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Delete public synonym
DROP PUBLIC SYNONYM sy_area1;
 
-- Delete private synonym under the current user
DROP SYNONYM sy_area2;
```

General Description
----

DROP OUTLINE is used to delete a stored outline.

Users must have the DROP ANY OUTLINE privilege to delete a stored outline.

In an ISC Distributed Cluster Deployment, when this statement is executed, the system will check for any uncommitted DDL statements. If there are any, an error will be reported and the statement must wait until the corresponding DDL is successfully committed before it can be executed.

Statement Definition
----

**drop\_outline::=**

```ebnf
= DROP [(PUBLIC)] OUTLINE outline_name.
```

### PUBLIC

Public mode, default value.

### outline\_name

The name of the OUTLINE to be deleted.

***Example***

```sql
-- Delete the specified OUTLINE
DROP OUTLINE ol_a;
```

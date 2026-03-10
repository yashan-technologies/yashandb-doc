General Description
----

DROP SEQUENCE is used to remove an existing sequence number generator.

When a sequence number generator is referenced by other database objects, deleting this sequence number generator may cause errors or invalidate the objects. For example:

* If a sequence number generator is referenced in the definition of a column field of a table with a DEFAULT value, an error YAS-02012 will be prompted when the DEFAULT is used to generate column field values after the sequence number generator is deleted.
* If a sequence number generator is specified as a column field in the definition of a view, that view becomes invalid after the sequence number generator is deleted.

Users cannot execute this statement in ISC Distributed Cluster Deployment.

Statement Definition
----

**drop sequence::=**

```ebnf+diagram
syntax::= DROP SEQUENCE [schema "."] sequence_name
```

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
DROP SEQUENCE seq_yashan1;
```

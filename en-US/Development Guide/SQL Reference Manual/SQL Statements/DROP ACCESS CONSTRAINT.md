General Description
----

DROP ACCESS CONSTRAINT is used to delete an AC object, and the data space occupied by that AC will also be fully released.

Statement Definition
----

**drop access constraint::=**

```ebnf
= DROP ACCESS CONSTRAINT ac_name.
```

### ac\_name

Specifies the name of the AC to be deleted.

***Example*** for LSC tables

```sql
DROP ACCESS CONSTRAINT ac_area;
```

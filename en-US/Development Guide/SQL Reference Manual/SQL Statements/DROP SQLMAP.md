General Description
----

DROP SQLMAP is used to delete an SQL mapping.

The user must have DBA privilege to delete an SQL mapping.

In ISC Distributed Cluster Deployment, when executing this statement, the system will check for any abnormal unrecovered DDL statements. If found, an error will be reported, and this statement can only be executed after the corresponding DDL is successfully recovered.

Statement Definition
----

**drop sqlmap::=**

```ebnf
= DROP SQLMAP sqlmap_name.
```

### sqlmap\_name

This statement is used to specify the name of the SQL mapping to be deleted.

***Example***

```sql
DROP SQLMAP map_branch;
```

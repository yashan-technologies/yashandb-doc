General Description
----

The DROP TABLESPACE SET statement is used to delete a tablespace set.

The statement has the following constraints:

- This statement is only applicable to ISC Distributed Cluster Deployment.
- A tablespace set that has been designated as the default tablespace set by a user cannot be deleted. To delete it, the user's DEFAULT TABLESPACE SET must first be changed using the [ALTER USER](ALTER USER) statement.
- The built-in tablespace sets USERS/USERS_AIM cannot be deleted.

In a ISC Distributed Cluster Deployment, when executing this statement, the system checks for any abnormal unrecoverable DDL statements. If such statements exist, an error will be reported, and this statement can only be executed after the corresponding DDL has been successfully recovered.

Statement Definition
----

**drop tablespace set::=**

```ebnf+diagram
syntax::= DROP TABLESPACE SET tablespace_set_name INCLUDING CONTENTS
```

### 1. tablespace\_set\_name

This statement specifies the name of the tablespace set to be deleted.

### 2. INCLUDING CONTENTS

This statement indicates that the tablespace set and all objects contained within it will also be deleted.

If the data files in the bucket that the tablespace set is mounted on have not completed archival cleanup, that bucket cannot be deleted. In this case, deleting the tablespace set will return an error.

***Example*** for ISC Distributed Cluster Deployment

```sql
DROP TABLESPACE SET tbs_tb INCLUDING CONTENTS;
DROP TABLESPACE SET mm_tss INCLUDING CONTENTS;
DROP TABLESPACE SET tss1 INCLUDING CONTENTS;
```

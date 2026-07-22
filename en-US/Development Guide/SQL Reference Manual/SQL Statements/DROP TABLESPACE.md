General Description
----

DROP TABLESPACE is used to delete a tablespace.

The statement has the following constraints:

- A DEFAULT TABLESPACE specified by any user cannot be deleted; it must first be changed using the [ALTER USER](ALTER USER) statement.
- A DEFAULT SWAP TABLESPACE specified by any instance cannot be deleted; it must first be changed using the ALTER SYSTEM SET DEFAULT_SWAP_TABLESPACE statement.
- A tablespace that has any database objects created cannot be deleted unless the INCLUDING CONTENTS statement is specified.
- If a partition table or partition index in the tablespace has at least one partition that specifies another tablespace in its storage parameters, the tablespace cannot be deleted even if the INCLUDING CONTENTS statement is specified.
- If there are foreign key constraints defined on objects in other tablespaces in the tablespace, the tablespace cannot be deleted even if the INCLUDING CONTENTS statement is specified, unless the CASCADE CONSTRAINT option is specified as well.
- Built-in tablespaces such as SYSTEM/SYSAUX/UNDO/SWAP/TEMPORARY cannot be deleted.
- Before deleting a local cache tablespace (unique to YAC/Distributed Cluster Deployment), ensure that disk cache is disabled ([ENABLE_DISKCACHE](../../../Reference Manual/Configuration Parameters.md#enable_diskcache) = FALSE).

In the event of node failures while deleting a tablespace in ISC Distributed Cluster Deployment, recovery measures can be found in the [User Tablespace Management](../../../Database Administration/Storage Management/Logical Space Management/Tablespace Management/General Tablespace Management) chapter.

In ISC Distributed Cluster Deployment, when executing this statement, the system checks for any uncommitted DDL statements. If such statements exist, an error will occur, and execution of this statement will have to wait until the corresponding DDL is successfully recovered.

In cluster deployment, if an instance failure occurs while deleting a tablespace, it may result in a leftover tablespace without data files. The state of the leftover tablespace will be OFFLINE and cannot be used normally; it can only be deleted using the DELETE statement.

Statement Definition
----

**drop tablespace::=**

```ebnf
= DROP TABLESPACE [IF EXISTS] tablespace_name [INCLUDING CONTENTS [(AND|KEEP) DATAFILES] [CASCADE CONSTRAINTS]].
```

### IF EXISTS

This clause is used to specify that before dropping the tablespace, the system should first check if it exists. If omitted, the system will not check; if the tablespace to be deleted does not exist, the system will return an error.

### tablespace\_name

This clause is used to specify the name of the tablespace to be deleted.

### INCLUDING CONTENTS

This clause is used to specify that the tablespace and all its contained objects will be deleted.

#### AND|KEEP DATAFILES

This specifies whether the corresponding data files are to be deleted or retained when the tablespace is deleted; the default is to retain the data files.

#### CASCADE CONSTRAINTS

When there are foreign key constraints defined on objects in other tablespaces that reference objects in this tablespace, these constraints will be deleted before the tablespace itself is deleted.

***Example***

```sql
DROP TABLESPACE yashan1;

DROP TABLESPACE IF EXISTS yashan2;

DROP TABLESPACE yashan3
INCLUDING CONTENTS
KEEP DATAFILES
CASCADE CONSTRAINTS;
```
